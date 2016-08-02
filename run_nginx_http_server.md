# 建立nginx http server


## Single Server Mode

### 建立Nginx服務

```
kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"
```

### 讓Nginx對外服務

#### Expose服務

expose deployment的方式，可以指定deployment name以及要expose的port來讓外部存取...

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

#### Expose服務時綁定本地IP

在本地端(自己建置的K8S集群)，由於沒有像Load Balancer的服務，因此需要設定使用主機的IP來對外服務，透過deployment的expose的方式，可以綁定本地的IP讓外部可以存取...

```
kubectl expose deployment nginx --port=80 --target-port=80 --external-ip=10.240.0.9
```

其中10.240.0.9會是host主機的IP...


#### 將服務掛載內部IP到GKE上

在GKE上，因為GCP上有LoadBalancer的服務，因此可以比較快速的直接調用Load Balancer來對外服務，步驟如下:


##### 對服務掛載LoadBalancer (外部IP)

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