# 使用Deployment部署

Depoy在K8S之中包含描述container內容以及replica數量等資訊，下面是一份deployment的範本，描述使用simpleweb這個image建置一個replica為1的服務，並開通container port為80...

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
        - name: NODE_ENV
          value: production
        - name: GET_HOSTS_FROM
          value: dns
```

## 參考

* K8S deployment doc: https://kubernetes.io/docs/resources-reference/v1.5/#deployment-v1beta1