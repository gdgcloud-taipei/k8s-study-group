

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