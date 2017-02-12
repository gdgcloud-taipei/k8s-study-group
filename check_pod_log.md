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


