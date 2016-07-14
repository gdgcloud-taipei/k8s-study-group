# 建立nginx http server


## Single Server Mode

### 建立Nginx服務

```
kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"
```

### 將服務掛載內部IP

```
kubectl expose deployment nginx-app --port=80 --name=nginx-http
```

```
kubectl get services
NAME         CLUSTER-IP      EXTERNAL-IP       PORT(S)    AGE
kubernetes   10.99.240.1     <none>            443/TCP    1d
nginx-http   10.99.255.30    <none>            80/TCP     1h
```
此時，可以直接登入該container所掛載的位置，並且透過docker ip來做存取，例如：

```
curl http://10.99.255.30:80
```

### 對服務掛載LoadBalancer (外部IP)

```
kubectl expose deployment nginx-app --type="LoadBalancer"
```

檢視服務掛載IP後：

```
kubectl get services
NAME         CLUSTER-IP      EXTERNAL-IP       PORT(S)    AGE
kubernetes   10.99.240.1     <none>            443/TCP    1d
nginx-app    10.99.249.119   130.211.254.181   80/TCP     2m
```

此時，service已經有外部的IP位置，可以透過外部IP來存取：

```
curl http://130.211.254.181:80
```


ssh peihsinsu@104.155.210.209 -A -p 22 -L 15984:localhost:5984