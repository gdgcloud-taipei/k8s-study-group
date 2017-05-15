# 使用secret

secret是一份yaml文件，其中密文透過base64進行加密，待import進k8s之後，可以透過secret volume的方式掛載給其他Pod使用，Pod中所見的資料即會是一份文字檔案。

## Secret文件

### 準備加密字串

首先，需要把要加密的字串以base64的方式先準備好

```
# echo -n "simonsu" | base64
c2ltb25zdQ==
# echo -n "simonsu.mail@gmail.com" | base64
c2ltb25zdS5tYWlsQGdtYWlsLmNvbQ==
```

如果要把檔案整個加密，也可以用上述方式...

```
cat key.json | base64
```

但是當檔案比較常時候，會有斷行情況發生，因此可以透過"-w 0"來去除斷行...

```
cat key.json | base64 -w 0
```

### Secret yaml文件

\[file: secret.yaml\]

```
apiVersion: v1
kind: Secret
metadata:
  name: test-secret
data:
  data-1: c2ltb25zdQ==
  data-2: c2ltb25zdS5tYWlsQGdtYWlsLmNvbQ==
```

### 上傳secret文件到k8s

```
kubectl create -f secret.yaml
```

## 建立使用Secret的Pod （使用volume）

### Pod文件

\[file: secret-pod.yaml\]

```
apiVersion: v1
kind: Pod
metadata:
  name: secret-test-pod
spec:
  containers:
    - name: test-container
      image: peihsinsu/simpleweb
      volumeMounts:
          # name must match the volume name below
          - name: secret-volume
            mountPath: /etc/secret-volume
  volumes:
    - name: secret-volume
      secret:
        secretName: test-secret
  restartPolicy: Never
```

### 建立Pod

```
kubectl create -f secret-pod.yaml
```

## 檢視與測試

### 檢視k8s上secret文件

使用get secret檢視，輸出成yaml格式

```
# kubectl get secret test-secret -o yaml
apiVersion: v1
data:
  data-1: c2ltb25zdQ==
  data-2: c2ltb25zdS5tYWlsQGdtYWlsLmNvbQ==
kind: Secret
metadata:
  creationTimestamp: 2016-09-28T06:47:02Z
  name: test-secret
  namespace: default
  resourceVersion: "3939"
  selfLink: /api/v1/namespaces/default/secrets/test-secret
  uid: 5cd889b0-8547-11e6-a8fc-42010af00086
type: Opaque
```

使用describe檢視：

```
# kubectl describe secret test-secret
Name:        test-secret
Namespace:    default
Labels:        <none>
Annotations:    <none>

Type:    Opaque

Data
====
data-1:    7 bytes
data-2:    22 bytes
```

### 從Pod內檢視Secret文件

取得pod的名稱

```
# kubectl get pods
NAME              READY     STATUS    RESTARTS   AGE
secret-test-pod   1/1       Running   0          6m
```

登入pod檢視所帶入的secret檔案資料

```
# kubectl exec  secret-test-pod -it sh
/app # 
/app # cat /etc/secret-volume/data-1
simonsu/app #
```

## 將 secret 用於 env

承上例，我們將NAME 和EMAIL兩個環境變數從secret取得，並輸出至stdout:

使用方式和 configMaps 一致。

```
$ cat secretByEnv-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-by-env
spec:
  containers:
  - name: busydayeasylife
    image: gcr.io/google_containers/busybox:1.24
    command:
    - /bin/sh
    - -c
    - --
    args:
    - "while [ true ]; do echo $NAME; echo $EMAIL; sleep 30; done;"
    env:
    - name: NAME
      valueFrom:
        secretKeyRef:
          name: test-secret
          key: data-1
    - name: EMAIL
      valueFrom:
        secretKeyRef:
          name: test-secret
          key: data-2
          
$ kubectl apply -f secretByEnv-pod.yaml 
```

### 檢視

```
$ kubectl logs secret-by-env
simonsu
simonsu.mail@gmail.com
```

## 參考

* 官方secret walkthrough: [http://kubernetes.io/docs/user-guide/secrets/walkthrough/](http://kubernetes.io/docs/user-guide/secrets/walkthrough/)
* 官方secret說明：[http://kubernetes.io/docs/user-guide/secrets/\#decoding-a-secret](http://kubernetes.io/docs/user-guide/secrets/#decoding-a-secret)
* 官方secret concept: [https://kubernetes.io/docs/concepts/configuration/secret/\#using-secrets-as-environment-variables](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-environment-variables)



