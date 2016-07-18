# 使用docker-compose安裝kubernetes


## 安裝

Step One: Run etcd

```
docker run --net=host \
  -d gcr.io/google_containers/etcd:2.0.12 \
  /usr/local/bin/etcd \
  --addr=127.0.0.1:4001 \
  --bind-addr=0.0.0.0:4001 \
  --data-dir=/var/etcd/data
```

Step Two: Run the master

```
docker run \
    --volume=/:/rootfs:ro \
    --volume=/sys:/sys:ro \
    --volume=/dev:/dev \
    --volume=/var/lib/docker/:/var/lib/docker:ro \
    --volume=/var/lib/kubelet/:/var/lib/kubelet:rw \
    --volume=/var/run:/var/run:rw \
    --net=host \
    --pid=host \
    --privileged=true \
    -d \
    gcr.io/google_containers/hyperkube:v1.0.1 \
    /hyperkube kubelet --containerized \
    --hostname-override="127.0.0.1" \
    --address="0.0.0.0" \
    --api-servers=http://localhost:8080 \
    --config=/etc/kubernetes/manifests
```

This actually runs the kubelet, which in turn runs a pod that contains the other master components.

If you running Docker for Mac/Windows, please add shared folder on `/var/lib/kubelet/`.


Step Three: Run the service proxy

```
docker run -d --net=host \
  --privileged gcr.io/google_containers/hyperkube:v1.0.1 \
  /hyperkube proxy \
  --master=http://127.0.0.1:8080 \
  --v=2
```

## 安裝kubectl

Linux:
```
wget https://storage.googleapis.com/kubernetes-release/release/v1.0.1/bin/linux/amd64/kubectl && mv kubectl /usr/bin/ && chmod u+x /usr/bin/kubectl
```

Mac:

```
wget https://storage.googleapis.com/kubernetes-release/release/v1.0.1/bin/darwin/amd64/kubectl && mv kubectl /usr/bin/ && chmod u+x /usr/bin/kubectl
```

除了直接下載執行檔案，也可以使用 Google Cloud SDK 所提供的工具來安裝：


安裝 Google Cloud SDK:

```
curl https://sdk.cloud.google.com | bash
```

透過 Google Cloud SDK 安裝 Kubectl

```
gcloud components install kubectl
```


## 執行

建立pods
```
kubectl -s http://127.0.0.1:8080 run nginx --image=nginx --port=80
```

設定對外port

```
kubectl -s http://127.0.0.1:8080 expose rc nginx --port=80
```

或指定對外port

```
kubectl -s http://127.0.0.1:8080 expose rc nginx --port=80 --target-port=8000
```

## Use Compose

File: k8s-swarm.xml

```
etcd:
  image: gcr.io/google_containers/etcd:2.0.13
  container_name: etcd
  command: ['/usr/local/bin/etcd', '--bind-addr=0.0.0.0:4001', '--data-dir=/var/etcd/data']

apiserver:
  image: gcr.io/google_containers/hyperkube:v1.0.7
  container_name: apiserver
  ports:
    - "8080:8080"
  command: ["/hyperkube", "apiserver", "--service-cluster-ip-range=172.17.17.1/24", "--address=0.0.0.0", "--etcd_servers=http://etcd:4001", "--cluster_name=kubernetes", "--v=2"]

controller:
  image: gcr.io/google_containers/hyperkube:v1.0.7
  command: ["/hyperkube", "controller-manager", "--address=0.0.0.0", "--master=http://apiserver:8080", "--v=2"]
  environment:
    - "affinity:container==*apiserver*"

scheduler:
  image: gcr.io/google_containers/hyperkube:v1.0.7
  command: ["/hyperkube", "scheduler", "--address=0.0.0.0", "--master=http://apiserver:8080", "--v=2"]
  environment:
    - "affinity:container==*apiserver*"

kubelet:
  image: gcr.io/google_containers/hyperkube:v1.0.7
  command: ['/hyperkube', 'kubelet', '--containerized' , '--api_servers=http://apiserver:8080', '--v=2', '--address=0.0.0.0', '--enable_server']
  volumes:
    - /:/rootfs:ro
    - /sys:/sys:ro
    - /dev:/dev
    - /var/run/docker.sock:/var/run/docker.sock
    - /var/lib/docker/:/var/lib/docker:ro
    - /var/lib/kubelet/:/var/lib/kubelet:rw
    - /var/run:/var/run:rw
  privileged: true
  # A kubelet shouldn't run alongside another kubelet - One privileged kubelet per node
  environment:
    - "affinity:container!=*kubelet*"

proxy:
  image: gcr.io/google_containers/hyperkube:v1.0.7
  command: ['/hyperkube', 'proxy', '--master=http://apiserver:8080', '--v=2']
  privileged: true
  # A proxy should run alongside another kubelet but not alongside another proxy
  environment:
    - "affinity:container==*kubelet*"
    - "affinity:container!=*proxy*"
```

Start the docker-compose.yml

```
docker-compose --x-networking -f k8s-swarm.yml up -d
```


## Reference

* 官方說明：http://kubernetes.io/v1.1/docs/getting-started-guides/docker.html


## Others
隨著版本發展，不同的版本可能會遇到不同的問題，下面簡單列出幾個可能的問題給大家參考：

* Mac在搭配VirtualBox時候會有一些問題，例如新版的VirtualBox不支援原本boot2docker的ip forward，因此在boot2docker中有無法連線的問題

