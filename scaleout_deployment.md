# 在Deployment部署下Scaleout

Deployment部署完成後，通常維運人員需要依據Pod的狀態來做適當的instance數量調整... 下面是個簡單的Deployment檔案，其中replicas預設為1個instance。

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: myweb
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: myweb
        tier: myweb
    spec:
      containers:
      - name: myweb
        image: peihsinsu/simpleweb
        ports:
        - containerPort: 80
        env:
        - name: PORT
          value: "80"
        - name: GET_HOSTS_FROM
          value: dns
```

接下來我們可以部署這個設定檔(將上面設定檔儲存為: myweb.yaml)

```
# kubectl create -f myweb.yaml
```

```
# kubectl get pods,deploy
NAME                             READY     STATUS              RESTARTS   AGE
po/myweb-843577423-40749         0/1       ContainerCreating   0          4s

NAME                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/myweb        1         1         1            0           4s
```

部署好之後，應該會看到該deployment會有一個instance...，此時可以透過kubectl scale來重新設定replica數量：

```
# kubectl scale --replicas=3 deploy/myweb
```
```
# kubectl get pods,deploy
NAME                             READY     STATUS             RESTARTS   AGE
po/myweb-843577423-32g2n         1/1       Running            0          3m
po/myweb-843577423-40749         1/1       Running            0          4m
po/myweb-843577423-kwxh1         1/1       Running            0          3m

NAME                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/myweb        3         3         3            3           4m
```

如果此時要做scale in，則可以直接將replica數量設小，instance會砍掉多餘的，留下足夠的replica來服務...