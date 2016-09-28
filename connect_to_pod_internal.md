# 連線到Pod檢視內部

當服務建立好後，常常因為debug的原因，會需要連線到pod內查詢一些狀況，我們可以透過kubectl exec的方式連線到pod內檢視：

收先需要取得pod的名稱

```
# kubectl get pods
NAME              READY     STATUS    RESTARTS   AGE
secret-test-pod   1/1       Running   0          6m
```

然後透過exec來登入，並指定"-it"(interactive + terminal模式)，以及給定登入後的shell為sh...

```
# kubectl exec  secret-test-pod -it sh
/app # 
```

接下來就可以執行需要的指令了...

如果您的Pod是有兩個以上container的組成，可以透過-c帶入container name(設定在yaml裡面的名稱)來連線：

```
# kubectl exec -it mypod -c ap bash
```

如果未指定，則會以yaml file中第一個container為主要container連入...

