# 使用kube-deploy建立multi-node cluster

這個專案(http://github.com/kubernetes/kube-deploy)大概是kubernetes的安裝上數一數二簡單的方式...  只要多玩幾次，應該可以很快上手唷～

## 建立主機

首先建立cluster用的主機，並安裝docker...(這邊不再贅述安裝過程)，目前測試過成功的安裝包括：

* GCE CentOS7
* GCE CoreOS stable
* GCE Ubuntu 15

其中CentOS7可以完全不修改的情況下照著流程跑完，其他的環境可以修改一小部分安裝腳本來跑完...


## 安裝

### 安裝master

進到master主機，執行下面指令

```
git clone https://github.com/kubernetes/kube-deploy
cd kube-deploy/docker-multinode
./master.sh
```

### 安裝worker

進到worker主機，執行下面指令

```
git clone https://github.com/kubernetes/kube-deploy
cd kube-deploy/docker-multinode
export MASTER_IP=[master_ip]
./worker.sh
```

### 檢查

最後啟動完，在master與worker上應該會看到如下的程序列表...

#### Master Processes

```
docker ps
CONTAINER ID        IMAGE                                                        COMMAND                  CREATED             STATUS              PORTS               NAMES
b5abf6fa4452        gcr.io/google_containers/exechealthz-amd64:1.1               "/exechealthz '-cmd=n"   59 minutes ago      Up 59 minutes                           k8s_healthz.92b64eae_kube-dns-v17.1-0bxw2_kube-system_11900aae-5fa4-11e6-b1a0-42010af00009_d81566f1
5f175ce7e83b        gcr.io/google_containers/kube-dnsmasq-amd64:1.3              "/usr/sbin/dnsmasq --"   59 minutes ago      Up 59 minutes                           k8s_dnsmasq.9cc9760b_kube-dns-v17.1-0bxw2_kube-system_11900aae-5fa4-11e6-b1a0-42010af00009_b8c0d776
35fcdba200d8        gcr.io/google_containers/kubedns-amd64:1.5                   "/kube-dns --domain=c"   About an hour ago   Up 59 minutes                           k8s_kubedns.6b818f53_kube-dns-v17.1-0bxw2_kube-system_11900aae-5fa4-11e6-b1a0-42010af00009_4259f590
0212754549a0        gcr.io/google_containers/kubernetes-dashboard-amd64:v1.1.0   "/dashboard --port=90"   About an hour ago   Up About an hour                        k8s_kubernetes-dashboard.f96ccb63_kubernetes-dashboard-v1.1.0-7wupe_kube-system_118a6eba-5fa4-11e6-b1a0-42010af00009_8c6719bc
e93ee4079ab1        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 About an hour ago   Up About an hour                        k8s_POD.a6b39ba7_kube-dns-v17.1-0bxw2_kube-system_11900aae-5fa4-11e6-b1a0-42010af00009_9b594f6f
c131cd01bc56        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 About an hour ago   Up About an hour                        k8s_POD.2225036b_kubernetes-dashboard-v1.1.0-7wupe_kube-system_118a6eba-5fa4-11e6-b1a0-42010af00009_9d1b3989
3a22fbb92e12        gcr.io/google_containers/hyperkube-amd64:v1.3.4              "/copy-addons.sh"        About an hour ago   Up About an hour                        k8s_kube-addon-manager-data.3b72199f_kube-addon-manager-10.240.0.9_kube-system_29f6ff0d8867cc4a121f6442895ecd07_f321e682
3ef310f4d228        gcr.io/google-containers/kube-addon-manager-amd64:v4         "/opt/kube-addons.sh"    About an hour ago   Up About an hour                        k8s_kube-addon-manager.a97f8ca4_kube-addon-manager-10.240.0.9_kube-system_29f6ff0d8867cc4a121f6442895ecd07_3db38dd0
ccdd4998d981        gcr.io/google_containers/hyperkube-amd64:v1.3.4              "/hyperkube scheduler"   About an hour ago   Up About an hour                        k8s_scheduler.33afcc1_k8s-master-10.240.0.9_kube-system_c25065f253ce380787b6c4a8ca9badb5_9aad247d
807dd8ff0b84        gcr.io/google_containers/hyperkube-amd64:v1.3.4              "/hyperkube apiserver"   About an hour ago   Up About an hour                        k8s_apiserver.e1a5bf74_k8s-master-10.240.0.9_kube-system_c25065f253ce380787b6c4a8ca9badb5_6aa3f724
27429ab818be        gcr.io/google_containers/hyperkube-amd64:v1.3.4              "/hyperkube controlle"   About an hour ago   Up About an hour                        k8s_controller-manager.72ff5161_k8s-master-10.240.0.9_kube-system_c25065f253ce380787b6c4a8ca9badb5_03eb94d9
b3655efaf6ac        gcr.io/google_containers/hyperkube-amd64:v1.3.4              "/setup-files.sh IP:1"   About an hour ago   Up About an hour                        k8s_setup.39693812_k8s-master-10.240.0.9_kube-system_c25065f253ce380787b6c4a8ca9badb5_c740fd19
d346fa48600f        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 About an hour ago   Up About an hour                        k8s_POD.d8dbe16c_k8s-master-10.240.0.9_kube-system_c25065f253ce380787b6c4a8ca9badb5_e3014663
3a574093cc0c        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 About an hour ago   Up About an hour                        k8s_POD.d8dbe16c_kube-addon-manager-10.240.0.9_kube-system_29f6ff0d8867cc4a121f6442895ecd07_91be59bb
1b5a60af23b1        gcr.io/google_containers/hyperkube-amd64:v1.3.4              "/hyperkube proxy --m"   About an hour ago   Up About an hour                        k8s_kube-proxy.a4344856_k8s-proxy-10.240.0.9_kube-system_d03a8e5b4dd1dbcc1ae1a06eb6c88afb_b596bc54
563acb2f8f5b        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 About an hour ago   Up About an hour                        k8s_POD.d8dbe16c_k8s-proxy-10.240.0.9_kube-system_d03a8e5b4dd1dbcc1ae1a06eb6c88afb_587709fd
3b56a88f302f        gcr.io/google_containers/hyperkube-amd64:v1.3.4              "/hyperkube kubelet -"   About an hour ago   Up About an hour                        kube_kubelet_80b03
```

#### Worker Processes

```
docker ps
CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS              PORTS               NAMES
f812c56ec7e7        gcr.io/google_containers/hyperkube-amd64:v1.3.4   "/hyperkube proxy --m"   5 minutes ago       Up 4 minutes                            kube_proxy_acf16
cd9b2387c081        gcr.io/google_containers/hyperkube-amd64:v1.3.4   "/hyperkube kubelet -"   5 minutes ago       Up 5 minutes                            kube_kubelet_a5ddd
```


## 安裝有問題 - not shared mount

如果遇到執行master.sh或slave.sh時候，有/var/lib/kubelet不是shared mount filesystem時候，可以透過下面方式解決

Solution 1: 將該mount point改為shared mount...

```
DOCKER_CONF=$(systemctl cat docker | head -1 | awk '{print $2}') 
sed -i.bak 's/^\(MountFlags=\).*/\1shared/' $DOCKER_CONF 
systemctl daemon-reload 
systemctl restart docker

mkdir -p /var/lib/kubelet 
mount --bind /var/lib/kubelet /var/lib/kubelet 
mount --make-shared /var/lib/kubelet
```

Solution 2: 直接移除啟動腳本的shared flag

```
vi kube-deploy/docker-multinode/common.sh
```

修改mount /var/lib/kubelet那段的shared flag

```
  ...
    -v /var/lib/kubelet:/var/lib/kubelet:shared \ 
  ...
```

修改為：

```
  ...
    -v /var/lib/kubelet:/var/lib/kubelet \
  ...
```

## 安裝有問題 - docker not start

可能是在某些環境的docker啟動時間不太一定，曾經在ubuntu與coreos上遇到最後docker run會有docker為執行的錯誤...後來透過在啟動前停止3秒鐘來避免找不到環境的問題...

```
...
kube::multinode::start_k8s_master() {
  kube::log::status "Launching Kubernetes master components..."

  kube::multinode::make_shared_kubelet_dir
  
  # Stop 3 seconds
  sleep 3

  docker run -d \
    --net=host \
    --pid=host \
    --privileged \
    --restart=${RESTART_POLICY} \
    --name kube_kubelet_$(kube::helpers::small_sha) \
    ...

```

