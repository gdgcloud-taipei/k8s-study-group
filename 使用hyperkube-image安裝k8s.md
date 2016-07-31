# 使用hyperkube image安裝docker

首先，如果尚未有docker環境，可以透過docker machine啟動Google Compute Engine上的主機服務... 如未指定image參數，預設會使用ubuntu 15的image環境...

```
docker-machine create --driver google \
 --google-project sunny-573 \
 --google-zone asia-east1-a \
 --google-machine-type f1-micro \
 simon-docker
```

您也可以透過指定image位置與硬碟大小來進一步設定您的環境

```
docker-machine create --driver google \ 
  --google-project sunny-573 \
  --google-zone asia-east1-a \
  --google-machine-type f1-micro \
  --google-disk-size 300 \
  --google-machine-image projects/debian-cloud/global/images/debian-8-jessie-v20160718 \
  simon-docker2
```

## 環境準備

### 先設定要安裝的K8S版本與環境參數

下面指定要安裝stable的版本，並且為amd64的CPU Arch

```
export K8S_VERSION=$(curl -sS https://storage.googleapis.com/kubernetes-release/release/stable.txt)
export ARCH=amd64
```

如欲指定其他環境，可以參考：http://kubernetes.io/docs/getting-started-guides/docker/

### 準備K8S所會用到的Shared Mount

Shared Mount是為了讓K8S在啟動Container時候有一個交換資訊的空間，

```
DOCKER_CONF=$(systemctl cat docker | head -1 | awk '{print $2}') 
sed -i.bak 's/^\(MountFlags=\).*/\1shared/' $DOCKER_CONF 
systemctl daemon-reload 
systemctl restart docker

mkdir -p /var/lib/kubelet 
mount --bind /var/lib/kubelet /var/lib/kubelet 
mount --make-shared /var/lib/kubelet
```

## 啟動hyperkube

```
docker run -d \
 --volume="/sys:/sys:rw" \
 --volume="/var/lib/docker/:/var/lib/docker:rw" \
 --volume="/var/lib/kubelet/:/var/lib/kubelet:rw,shared" \
 --volume="/var/run:/var/run:rw" \
 --net=host \
 --pid=host \
 --privileged \
 gcr.io/google_containers/hyperkube-${ARCH}:${K8S_VERSION} \
 /hyperkube kubelet \
 --hostname-override=127.0.0.1 \
 --api-servers=http://localhost:8080 \
 --config=/etc/kubernetes/manifests \
 --cluster-dns=10.0.0.10 \
 --cluster-domain=cluster.local \
 --allow-privileged --v=2
```

啟動後，hyperkube會開始將k8s的服務帶起來，過不了多久，就可以完整地看到所有的服務以container的方式跑起來...

```
# docker ps
CONTAINER ID        IMAGE                                                        COMMAND                  CREATED             STATUS              PORTS               NAMES
edbc6a726dac        gcr.io/google_containers/exechealthz-amd64:1.0               "/exechealthz '-cmd=n"   37 minutes ago      Up 37 minutes                           k8s_healthz.d2cd4ee4_kube-dns-v17-23bo8_kube-system_83cd7e67-5749-11e6-b487-42010af00009_7877437b
878e1f9f4b59        gcr.io/google_containers/kubernetes-dashboard-amd64:v1.1.0   "/dashboard --port=90"   37 minutes ago      Up 37 minutes                           k8s_kubernetes-dashboard.d025cba6_kubernetes-dashboard-v1.1.0-01267_kube-system_83e4f554-5749-11e6-b487-42010af00009_4ea6ddc9
c453e5151848        gcr.io/google_containers/kube-dnsmasq-amd64:1.3              "/usr/sbin/dnsmasq --"   37 minutes ago      Up 37 minutes                           k8s_dnsmasq.596764e_kube-dns-v17-23bo8_kube-system_83cd7e67-5749-11e6-b487-42010af00009_821f379d
4cf710c4ba5d        gcr.io/google_containers/kubedns-amd64:1.5                   "/kube-dns --domain=c"   37 minutes ago      Up 37 minutes                           k8s_kubedns.a968e9f_kube-dns-v17-23bo8_kube-system_83cd7e67-5749-11e6-b487-42010af00009_9421e348
d7e47bc6fda7        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 37 minutes ago      Up 37 minutes                           k8s_POD.2225036b_kubernetes-dashboard-v1.1.0-01267_kube-system_83e4f554-5749-11e6-b487-42010af00009_ad7a1dca
85a22803ce2a        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 37 minutes ago      Up 37 minutes                           k8s_POD.a6b39ba7_kube-dns-v17-23bo8_kube-system_83cd7e67-5749-11e6-b487-42010af00009_bbe451a2
b0e98a6e20c3        gcr.io/google_containers/hyperkube-amd64:v1.3.3              "/hyperkube scheduler"   37 minutes ago      Up 37 minutes                           k8s_scheduler.ddfcc0_k8s-master-127.0.0.1_kube-system_7f2a785eeb1a2126f50e0f788e7ccf5e_39b2a453
d72b0451309d        gcr.io/google_containers/hyperkube-amd64:v1.3.3              "/copy-addons.sh"        37 minutes ago      Up 37 minutes                           k8s_kube-addon-manager-data.38dd199e_kube-addon-manager-127.0.0.1_kube-system_8d3d40e6778a15f2e5c369728bbf7887_527e2771
5565a1dfa71d        gcr.io/google_containers/hyperkube-amd64:v1.3.3              "/hyperkube apiserver"   37 minutes ago      Up 37 minutes                           k8s_apiserver.1fe5bfde_k8s-master-127.0.0.1_kube-system_7f2a785eeb1a2126f50e0f788e7ccf5e_f55ee494
e2c83249b9d5        gcr.io/google_containers/hyperkube-amd64:v1.3.3              "/hyperkube controlle"   37 minutes ago      Up 37 minutes                           k8s_controller-manager.6fc65160_k8s-master-127.0.0.1_kube-system_7f2a785eeb1a2126f50e0f788e7ccf5e_2ca29f74
09e3af4b24f1        gcr.io/google-containers/kube-addon-manager-amd64:v4         "/opt/kube-addons.sh"    37 minutes ago      Up 37 minutes                           k8s_kube-addon-manager.a97f8ca4_kube-addon-manager-127.0.0.1_kube-system_8d3d40e6778a15f2e5c369728bbf7887_bcb2ce56
c89eb82d7a3c        gcr.io/google_containers/hyperkube-amd64:v1.3.3              "/setup-files.sh IP:1"   37 minutes ago      Up 37 minutes                           k8s_setup.366b3811_k8s-master-127.0.0.1_kube-system_7f2a785eeb1a2126f50e0f788e7ccf5e_d5112b66
abb0a979aced        gcr.io/google_containers/etcd-amd64:2.2.5                    "/usr/local/bin/etcd "   37 minutes ago      Up 37 minutes                           k8s_etcd.7e3232d1_k8s-etcd-127.0.0.1_kube-system_ca962e9cb8c3150d26a49b9c1cddcd29_6fe87235
e8731e91f79d        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 37 minutes ago      Up 37 minutes                           k8s_POD.d8dbe16c_k8s-master-127.0.0.1_kube-system_7f2a785eeb1a2126f50e0f788e7ccf5e_77454587
cb3533243fc6        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 37 minutes ago      Up 37 minutes                           k8s_POD.d8dbe16c_k8s-etcd-127.0.0.1_kube-system_ca962e9cb8c3150d26a49b9c1cddcd29_1f6aa898
ab2fe580eefe        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 37 minutes ago      Up 37 minutes                           k8s_POD.d8dbe16c_kube-addon-manager-127.0.0.1_kube-system_8d3d40e6778a15f2e5c369728bbf7887_25c525df
05da6e1fde25        gcr.io/google_containers/hyperkube-amd64:v1.3.3              "/hyperkube proxy --m"   37 minutes ago      Up 37 minutes                           k8s_kube-proxy.a1034855_k8s-proxy-127.0.0.1_kube-system_14f52877c50f53b9d116e130935a3ae8_8ef7cc5c
52027c53ce52        gcr.io/google_containers/pause-amd64:3.0                     "/pause"                 37 minutes ago      Up 37 minutes                           k8s_POD.d8dbe16c_k8s-proxy-127.0.0.1_kube-system_14f52877c50f53b9d116e130935a3ae8_3383eefa
63b4fcfd066d        gcr.io/google_containers/hyperkube-amd64:v1.3.3              "/hyperkube kubelet -"   37 minutes ago      Up 37 minutes                           compassionate_ritchie
root@simon-docker1:~#
```

## 連線與使用

首先，如果環境已經有對外網路，可以直接使用該對外網路的位置... 如果，未開放對外網路，可以透過反向代理來將遠端的port綁定到本機位置...

```
docker-machine ssh `docker-machine active` -N -L 8080:localhost:8080
```
如果綁定沒問題，只要透過curl即可測試

```
curl localhost:8080
```

只要看得到api結果，則代表設定無誤...


接下來設定kubectl的環境，在此設定遠端群組為"test-doc"，其中set-cluster中的位置，可以依照自己的需求，可以改變成server的對外位置...

```
kubectl config set-cluster test-doc --server=http://localhost:8080
kubectl config set-context test-doc --cluster=test-doc
kubectl config use-context test-doc
```

接下來就可以使用kubectl來建立自己的pods或其他服務了唷～


## 參考文獻

* kubernetes官方安裝文件：http://kubernetes.io/docs/getting-started-guides/docker/
* 有滿清楚的Shared Mount的說明：http://www.infoq.com/cn/articles/docker-kernel-knowledge-namespace-resource-isolation
