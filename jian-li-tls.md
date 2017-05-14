# 建立SSL Ingress

本篇討論透過Ingress建立HTTPS的連線服務...，在建置的過程需要先建立TLS的Secret物件，然後再附加到Ingress物件上，並指定TLS secret的名稱

## 建立TLS Secret物件

[secret-tls.yaml]
```
apiVersion: v1
data:
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key
kind: Secret
metadata:
  name: testsecret
  namespace: default
type: Opaque
```

其中secret的base64 encoded cert與key部分，可以透過下面方式來建置：

用cat取出key內容並給base64進行encode...，複製後貼到yaml檔案之tls.key後面

```
# cat mykey.key | base64
LS0tLS1CRUdJTiF...(skip)...UFJVVkFURSBLRVktLS1tLQo
```

用cat取出crt內容並給base64進行encode...，複製後貼到yaml檔案之tls.crt後面

```
# cat mycert.crt | base64
LS0tLS1CRUdJTi....(skip)....UFJKVkFURGBLRVktLS0tLQo
```

編輯完後，yaml檔案應該會像下面一樣：

```
apiVersion: v1
data:
  tls.crt: LS0tLS1CRUdJTi....(skip)....UFJKVkFURGBLRVktLS0tLQo
  tls.key: LS0tLS1CRUdJTiF...(skip)...UFJVVkFURSBLRVktLS1tLQo
kind: Secret
metadata:
  name: testsecret
  namespace: default
type: Opaque
```



編輯好TLS yaml檔案後，可以透過下面指令將該secret部署到k8s上...

```
# kubectl create -f secret-tls.yaml
```

## 建立Ingress物件

接著可以直接在ingress上加上該TLS設定，如下：

[ingress.yaml]
```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: myingress
spec:
  tls:
    - secretName: testsecret
  backend:
    serviceName: s1
    servicePort: 80
```

最後將該ingress yaml檔案部署上k8s...

```
# kubectl create -f ingress.yaml
```


```
# kubectl get secret,ingress
NAME                          TYPE                                  DATA      AGE
secrets/testsecret            Opaque                                2         1h

NAME                         HOSTS     ADDRESS          PORTS     AGE
myingress                    *         107.178.244.44   80, 443   1h
```