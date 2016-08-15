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

## 刪除測試資料

刪除所建立的nginx pod與service

```
kubectl delete pod nginx
kubectl delete service nginx
```

