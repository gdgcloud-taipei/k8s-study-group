# 使用Deployment更新Image版本

在K8S的特色中，以rolling update這個feature最吸引我，但是rolling update偏偏只能用在replication controller中，而目前官方建議以deployment的部屬方式來取代replication controller，言下之意也就是deployment有可以取代replication controller的方式...

```
apiVersion: v1
kind: Service
metadata:
  name: myweb
  labels:
    app: myweb
    tier: myweb
spec:
  ports:
  - port: 80
    targetPort: 80
  type: NodePort
  selector:
    app: myweb
    tier: myweb
  sessionAffinity: ClientIP
---
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
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: myweb-ingress
  annotations:
    kubernetes.io/ingress.global-static-ip-name: "ip-myweb-server"
spec:
  backend:
    serviceName: myweb
    servicePort: 80
```

執行image更新

```
kubectl set image deploy/myweb myweb=peihsinsu/simpleweb:v20170215.02
```