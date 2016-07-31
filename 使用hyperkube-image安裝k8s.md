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

Shared Mount是為了讓

```
DOCKER_CONF=$(systemctl cat docker | head -1 | awk '{print $2}') 
sed -i.bak 's/^\(MountFlags=\).*/\1shared/' $DOCKER_CONF 
systemctl daemon-reload 
systemctl restart docker

mkdir -p /var/lib/kubelet 
mount --bind /var/lib/kubelet /var/lib/kubelet 
mount --make-shared /var/lib/kubelet
```
