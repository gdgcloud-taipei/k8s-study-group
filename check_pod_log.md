# 查詢log

k8s提供像docker log一樣的指令，以下面的yaml檔案來看：

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  labels:
    app: mypod
spec:
  containers:
    - name: db
      image: couchdb
      ports:
        - containerPort: 5984
    - name: ap
      image: peihsinsu/simpleweb
      ports:
        - containerPort: 3000
```

當服務啟動後，可以透過：

```
# kubectl logs mypod ap
```

如果沒有指定pod裡面的container的話，會有下面的錯誤：

```
# kubectl logs mypod
Error from server: a container name must be specified for pod mypod, choose one of: [db ap]
```

Log的顯示與linux tail很像，支援"-f"的方式持續show pod的log

```
# kubectl logs -f mypod ap
```

Log在服務跑了一段時間後，可能會累積不少的Log資料，此時可以透過"--tail=30"的方式來指定要顯示最後的30行...

```
# kubectl logs -f --tail=30 mypod ap
```

最後，如果您使用kubectl get一個以上的項目時候，會發現在顯示的名稱部分，會加上"[type]/"的標示，讓我們知道這個項目是屬於pod[po], service[svc], deployment[deploy]... 等等

```
# kubectl get pods,svc,deploy,ingress
NAME                                 READY     STATUS    RESTARTS   AGE
po/iot-couchdb-3045641908-vep3i      1/1       Running   0          1d
po/iot-redis-235782042-s3lru         1/1       Running   1          42d
po/iot-server-1726068157-duno4       1/1       Running   0          1d

NAME                               CLUSTER-IP       EXTERNAL-IP       PORT(S)          AGE
svc/iot-couchdb                    10.163.240.73    <none>            5984/TCP         1d
svc/iot-redis                      10.163.243.169   <none>            6379/TCP         42d
svc/iot-server                     10.163.242.115   123.123.123.123    3000:30771/TCP   1d
svc/kubernetes                     10.163.240.1     <none>            443/TCP          47d

NAME                    DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/iot-couchdb      1         1         1            1           1d
deploy/iot-redis        1         1         1            1           42d
deploy/iot-server       1         1         1            1           1d
```

此時，如果要顯示pod的log，也可以直接使用:

```
# kubectl logs -f po/iot-server-1726068157-duno4
```


