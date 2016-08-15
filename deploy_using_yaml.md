# 使用YAML描述部署

K8s支援使用yaml或是json來描述要部署的服務，讓服務的建置更加方便。

## 建立單獨服務的container

下面是一個pod啟動單一container的描述檔，透過執行可以啟動一個nginx的pod，並且listen 80 port：

```
# k8s-1pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80
```

設定檔完成後，可以透過kubectl create並帶入該設定檔來直接啟動：

```
kubectl create -f k8s-1pod.yml
```

如pod已經啟動，需要expose port的話，可以透過kubectl expose來對外發布存取的port

```
kubectl expose -f k8s-1pod.yml
```

在expose之後，會啟動kuberbetes的service來允許port可以對外存取...

PS: 以上，label部分在未需要expose的時候是非必需存在，但如果需要expose時候，沒有設定metadata.labels，則會出現下面錯誤：

```
error: couldn't retrieve selectors via --selector flag or introspection: the pod has no labels and cannot be exposed
```

## 測試

啟動一台container測試所建立的nginx是否可以連線...

```
kubectl run busybox \
  --image=busybox \
  --restart=Never \
  --tty -i \
  --generator=run-pod/v1 \
  --env "POD_IP=$(kubectl get pod nginx -o go-template='{{.status.podIP}}')"
```

進入nginx之後，可以透過wget指令測試，應該會看到nginx的預設畫面

```
u@busybox$ wget -qO- http://$POD_IP
```

## 設定詳細說明

下面是yaml檔案設定的template，裡面有大致上常用的參數，設定上不妨可以參考這個template進行設定：

```
spec:
  containers:
    -
      args:
        - ""
      command:
        - ""
      env:
        -
          name: ""
          value: ""
      image: ""
      imagePullPolicy: ""
      name: ""
      ports:
        -
          containerPort: 0
          name: ""
          protocol: ""
      resources:
        cpu: ""
        memory: ""
  restartPolicy: ""
  volumes:
    -
      emptyDir:
        medium: ""
      name: ""
      secret:
        secretName: ""
```

### containers[]

* name: container的名稱，必須符合dns設定原則，並且不可重複
* image: docker image名稱
* containers物件中包含:
  * command[]: container啟動時候的entrypoint array，如果未指定，將會使用container image內所設定的預設entrypoint為主。本項設定無法更改。
  * args[]: container內部使用的command array包含給entrypoint使用的參數。如未指定，將使用container image裡面內建的cmd參數內容取代，此項設定也不允許變動。
  * env[]: 使用key:value格式設定的字串，將會作為container啟動後的環境變數，無法在事後更新。
    * name: container起動後的環境變數名稱，需要符合C_IDENTIFIER的規範。
    * value: container啟動後的環境變數值。
  * imagePullPolicy: image啟動的原則，可以接受下面兩種值:
    * Always
    * Never
    * IfNotPresentDefaults to Always if :latest tag is specified, or IfNotPresent otherwise. Cannot be updated.
  * ports[]: container所要expose的port(s)，不可更改。
    * containerPort: pod所要expose的port號。
    * name: service所要對應到的port的名稱，必須是DNS_LABEL並且為唯一存在。
    * protocol: 所要expose的port的協定，必須為TCP或UDP其中一個。預設為TCP。
  * resources: container所需要的運算資源限制...，包含:
    * cpu: 每個container所需要的佔有的CPU資源。預設為host上整個CPU資源都可以使用。預設的單位是m (e.g. 100m for one hundred milli-CPUs)。如果執行的host未有足夠的CPU資源執行，則該Pod將貴開不起來。
    * memory: 每個container所需要的佔有的記憶體資源。預設的單位是bytes (e.g. 100Mi for one hundred mebibytes)。如果執行的host未有足夠的CPU資源執行，則該Pod將貴開不起來。該設定無法更新。

### restartPolicy

Pod內部所有container重啟的策略，可使用的選項包括：
* Always
* OnFailure
* Never

### volumes[]

Pod中可以掛載的volume位置(以陣列方式存在)，必須指定給每個掛載的volume一個空間與名稱，且該volume可以使用volumeMount的方式附加在有設定該名稱的container下。volume可以用的型態包含：

* emptyDir: pod生命週期中可以分享的一個暫存的空間，包含:
  * medium: volume的儲存媒介形態，預設必須是個空的資串，或是"Memory"。
* hostPath: 一個事先存在的host檔案或是資料夾。通常使用在需要跟系統緊密結合且需要權限的系統daemon或是agent。需設定的內容包含:
  * path: The path of the directory on the host.
* secret: Secret to populate volume. Secrets are used to hold sensitive information, such as passwords, OAuth tokens, and SSH keys. Learn more from the docs on secrets. Contains:
  * secretName: The name of a secret in the pod’s namespace.
## 刪除測試資料

刪除所建立的nginx pod與service

```
kubectl delete pod nginx
kubectl delete service nginx
```

## 參考

* 官方K8S設定檔說明：http://kubernetes.io/docs/user-guide/pods/multi-container/#containers