# 使用Service為服務增加外部IP

服務的提供大部分是需要有個對外的IP位置，在K8S中，是透過Service這個元件來提供對外存取的入口點，在前面介紹過使用"kubectl"來建立Service

```
# kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"
# kubectl expose deployment nginx-app --port=80 --name=nginx-http
```

## 使用Service提供外部連線端口

在有了yaml描述檔後，我們也可以透過yaml檔案來描述deploy跟service:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-app
  labels:
    app: nginx-app
    tier: nginx-app
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: nginx-app
    tier: nginx-app
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-app
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx-app
        tier: nginx-app
    spec:
      containers:
        - name: nginx-app
          image: nginx-app
          ports:
          - containerPort: 80
```

將上面設定檔案存成"deploy.yaml"後，透過kubectl create即可將服務建立起來...

## 透過Load Balancer讓外部服務連線

同樣的設定，為了讓K8S可以結合Load Balancer的設定，我們可以透過指定type來提供指定的PORT讓外部連線：

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-app
  labels:
    app: nginx-app
    tier: nginx-app
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: nginx-app
    tier: nginx-app
  type: LoadBalancer
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-app
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx-app
        tier: nginx-app
    spec:
      containers:
        - name: nginx-app
          image: nginx-app
          ports:
          - containerPort: 80
```

如果設定無誤，透過kubectl create建立後，可以用kubectl get來查詢服務建立的狀況...

```
$ kubectl get pods,svc,deploy -o wide
NAME                        READY     STATUS    RESTARTS   AGE       IP          NODE
po/nginx-3075522020-hs3jv   1/1       Running   0          6s        10.0.0.18   gke-k8s-default-pool-d185c248-0pxq

NAME             CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE       SELECTOR
svc/kubernetes   10.3.240.1     <none>        443/TCP        1h        <none>
svc/nginx-app    10.3.251.111   <pending>     80:30039/TCP   6s        app=nginx-app,tier=nginx-app

NAME           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       CONTAINER(S)   IMAGE(S)   SELECTOR
deploy/nginx   1         1         1            1           6s        myweb          nginx      app=myweb,tier=myweb
```

其中service處的External IP會在expose後呈現<pending>的狀態，待一切開通無誤，則會顯示可以連線的IP位置...

