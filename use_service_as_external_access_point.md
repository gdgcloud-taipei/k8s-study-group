# 使用Service為服務增加外部IP

服務的提供大部分是需要有個對外的IP位置，在K8S中，是透過Service這個元件來提供對外存取的入口點，在前面介紹過使用"kubectl"來建立Service

```
# kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"
# kubectl expose nginx-app --type=LoadBalancer
```

這邊也可以透過yaml檔案來描述

```
apiVersion: v1
kind: Service
metadata:
  name: iot-couchdb
  labels:
    app: couchdb
    tier: iot-couchdb

spec:
  ports:
  - port: 5984
    targetPort: 5984
  selector:
    app: couchdb
    tier: iot-couchdb
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: iot-couchdb
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: couchdb
        tier: iot-couchdb
    spec:
      containers:
        - name: couchdb
          image: couchdb
          ports:
          - containerPort: 5984
          env:
          - name: COUCHDB_USER
            value: admin
          - name: COUCHDB_PASSWORD
            value: 1234qwer
          - name: GET_HOSTS_FROM
            value: dns
          volumeMounts:
          - name: db-storage
            mountPath: /usr/local/var/lib/couchdb
      volumes:
        - name: db-storage
          gcePersistentDisk:
            pdName: disk-iot-data
            fsType: ext4
```