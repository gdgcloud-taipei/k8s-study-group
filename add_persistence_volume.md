# 幫Pod增加Persistence Volume

在production的應用上，我們常會需要為container加上儲存空間來儲存資料，下面是使用kubernetes掛載volume的設定...

```
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-persistent-storage
      mountPath: /data/redis
  volumes:
  - name: redis-persistent-storage
    emptyDir: {}
```

首先需要建立volume

```
  volumes:
  - name: redis-persistent-storage
    emptyDir: {}
```

這段是替這個pod建立volume的方法，其中可以指定一個emptyDir作為volume的位置...

然後透過volumeMounts來掛載該建立起來的位置...

```
...(skip)
    volumeMounts:
    - name: redis-persistent-storage
      mountPath: /data/redis
```

在啟動這個pod之後，可以登入或使用docker inspect觀察，該pod會掛載所指定的空間上...

```
...(skip)
        "Mounts": [
            {
                "Source": "/var/lib/kubelet/pods/957756e9-62a6-11e6-8794-42010af0001d/volumes/kubernetes.io~empty-dir/redis-persistent-storage",
                "Destination": "/data/redis",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
```

## 掛載host位置

除了直接使用空的位置之外，
```
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-persistent-storage
      mountPath: /data/redis
  volumes:
  - name: redis-persistent-storage
    hostPath: {"path":"/data"}
```

使用inspect查看，會發現它是使用local driver來建立該磁碟以進行掛載...

```
...(skip)
"Mounts": [
 {
                "Name": "d22badc2596ae12cbe154d5f876a9f2a1b2fe19ee1316b6252405ad4458d994f",
                "Source": "/var/lib/docker/volumes/d22badc2596ae12cbe154d5f876a9f2a1b2fe19ee1316b6252405ad4458d994f/_data",
                "Destination": "/data",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
...(skip)
```

另外，也可以用docker volume來找到該磁碟...

```
# docker volume ls
DRIVER              VOLUME NAME
local               d22badc2596ae12cbe154d5f876a9f2a1b2fe19ee1316b6252405ad4458d994f
```