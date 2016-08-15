# 探討Pod中有多個container之狀況

在服務的配置上，有時候會需要把一個以上的container包在同一個Pod上(回憶一下，Pod是部屬的最小單位，可以有一個以上的container，但是不可切分，到時候會被deploy在同一台主機上)，在這樣的情況下，可以觀察一下container的狀況...

PS: 下面測試是在一台master與一台worker上，預設pod都會長在worker上...

```
# k8s-2pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: myweb
  labels:
    app: web
spec:
  containers:
    - name: db
      image: couchdb
      ports:
        - containerPort: 5984
    - name: ap
      image: peihsinsu/simpleweb
      ports:
        - containerPort: 3000
```

啟動服務...

```
kubctl create -f k8s-2pod.yml
```

分別登入，啟動的container確認一下IP位置

先登入k8s worker，可以用docker ps看到simpleweb與couchdb都長在這台worker上...

```
root@simon-k8s-worker1:~# docker ps
CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS              PORTS               NAMES
8982a7b6a806        peihsinsu/simpleweb                               "/usr/bin/npm start"     9 minutes ago       Up 9 minutes                            k8s_ap.b2d53365_myweb_default_1e96a60e-62a3-11e6-8794-42010af0001d_d81c6081
0c5d3d95760a        couchdb                                           "tini -- /docker-entr"   9 minutes ago       Up 9 minutes                            k8s_db.95762e7a_myweb_default_1e96a60e-62a3-11e6-8794-42010af0001d_a21af38c
5966e6515709        gcr.io/google_containers/pause-amd64:3.0          "/pause"                 9 minutes ago       Up 9 minutes                            k8s_POD.7af92679_myweb_default_1e96a60e-62a3-11e6-8794-42010af0001d_b0814640
555834a906e1        gcr.io/google_containers/hyperkube-amd64:v1.3.5   "/hyperkube proxy --m"   2 days ago          Up 25 hours                             kube_proxy_a913a
cf0b6bdfb91d        gcr.io/google_containers/hyperkube-amd64:v1.3.5   "/hyperkube kubelet -"   2 days ago          Up 25 hours                             kube_kubelet_a4b58
```

接下來，可以透過docker exec分別登入所啟動的兩個container，並檢查他們的IP...

先登入simpleweb...

```
root@simon-k8s-worker1:~# docker exec -it 8982a7b6a806 bash
rpc error: code = 2 desc = oci runtime error: exec failed: exec: "bash": executable file not found in $PATH
root@simon-k8s-worker1:~# docker exec -it 8982a7b6a806 sh
/app # ifconfig -a
eth0      Link encap:Ethernet  HWaddr 02:42:0A:01:54:02
          inet addr:10.1.84.2  Bcast:0.0.0.0  Mask:255.255.255.0
          inet6 addr: fe80::42:aff:fe01:5402%32668/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1432  Metric:1
          RX packets:2 errors:0 dropped:0 overruns:0 frame:0
          TX packets:3 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:168 (168.0 B)  TX bytes:258 (258.0 B)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1%32668/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

/app #
```

再登入couchdb...

```
root@simon-k8s-worker1:~# docker exec -it 0c5d3d95760a bash
root@myweb:/var/lib/couchdb# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
19: eth0@if20: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1432 qdisc noqueue state UP group default
    link/ether 02:42:0a:01:54:02 brd ff:ff:ff:ff:ff:ff
    inet 10.1.84.2/24 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:aff:fe01:5402/64 scope link tentative dadfailed
       valid_lft forever preferred_lft forever
root@myweb:/var/lib/couchdb#
```

上面會發現兩個container的服務所取得的internal ip address相同，這是因為pod內部使用了network binding的方式，讓pod內的container都用同一個IP位置，這樣在內部溝通上可以比較簡單... 可以透過docker inspect中搜尋"NetworkMode"來看到這部分的設定...

```
# docker inspect 8982a7b6a806
[
    {
        "Id": "8982a7b6a806d472255e38cef164ae08b0080fd0257aa376d02a0cfda66cc960",
        "Created": "2016-08-15T04:45:46.841268011Z",
        "Path": "/usr/bin/npm",
        ....(skip)
        "HostConfig": {
            "Binds": [
                "/var/lib/kubelet/pods/1e96a60e-62a3-11e6-8794-42010af0001d/volumes/kubernetes.io~secret/default-token-2h2ok:/var/run/secrets/kubernetes.io/serviceaccount:ro",
                "/var/lib/kubelet/pods/1e96a60e-62a3-11e6-8794-42010af0001d/etc-hosts:/etc/hosts",
                "/var/lib/kubelet/pods/1e96a60e-62a3-11e6-8794-42010af0001d/containers/ap/d81c6081:/dev/termination-log"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "container:5966e65157097b45d6acf56d26924421f9a86f031b134b74d07a2ffab9b0d66c",
           ....(skip)
    }
]
```

這個動作相當於在docker 1.12中使用"--network container:[id or name]"來綁定ip到另一個container...

```
docker run -d --name db couchdb
docker run -d --network container:db peihsinsu/simpleweb
```

## 參考
* 關於network binding的官方文件： https://docs.docker.com/engine/reference/run/#/network-container
