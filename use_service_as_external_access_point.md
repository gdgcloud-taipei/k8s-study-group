# 使用Service為服務增加外部IP

服務的提供大部分是需要有個對外的IP位置，在K8S中，是透過Service這個元件來提供對外存取的入口點，在前面介紹過使用"kubectl"來建立Service

```
# kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"
# kubectl expose deployment nginx-app --port=80 --name=nginx-http
```

在有了yaml描述黨後，我們也可以透過yaml檔案來描述deploy跟service:

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