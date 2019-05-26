# 單機版 K8S - minikube

kubernetes.io 發佈了一個供本地端單機跑 Kubernetes 最精簡的環境 minikube，透過 minikube 可以很快速地使用與測試功能唷～

## 安裝 Hypervisor

推薦使用 [Virtual Box](https://www.virtualbox.org/wiki/Downloads)。

依據不同的作業系統，也可以使用 KVM 或是 VMware。

## 安裝minikube

安裝的部分要看您的主機是哪種作業系統，下面是 Mac OS 的安裝方式

```
$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 \ 
  && chmod +x minikube \ 
  && sudo mv minikube /usr/local/bin/
```

其他的操作部分都是使用 `kubectl`，可以參考[文件](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)。

## 其他一功能

透過 minikube 的指令，可以看到在 Mac OS 上，其實是使用 Virtualbox 來啟動裝有 Kubernetes 的 boot2docker 環境。

```
# ps -ef | grep minikube
 502 14260 14236 0 11:49上午 ?? 21:11.73 /Applications/VirtualBox.app/Contents/MacOS/VBoxHeadless --comment minikubeVM --startvm cb568998-d8b4-40d4-a6e7-c92aea5aee3d --vrde config
 502 18986 4258 0 3:59下午 ttys008 0:00.00 grep --color=auto minikube
```

minikube 除了提供 Kubernetes 環境之外，而且也連結了這些環境提供一些不錯的操控方式，這邊紀錄幾個看到還不錯用的功能。

### 檢視版本資訊

查看 minikube 版本

```
$ minikube version
minikube version: v1.0.1
```

### minikube 的儀表板

```
$ minikube dashboard
```

此時，桌面會跳出瀏覽器呈現 Kubernetes 的 dashboard。這版本的 dashboard 已經提供了直接在網頁介面部署 App 的功能，除此之外，也可以透過 yaml 檔來作部署的動作。

![minikube dashboard](assets/minikube-dashboard.png)

### minikube docker env

這個指令提供顯示目前 minikube 的 docker 環境的連線參數，可以讓外部的 docker client 透過這些參數來連線 minikube 環境做進階操控。

```
$ minikube docker-env
export DOCKER_TLS_VERIFY=1
export DOCKER_HOST=tcp://192.168.99.100:2376
export DOCKER_CERT_PATH=/Users/peihsinsu/.minikube/certs
# Run this command to configure your shell:
# eval $(minikube docker-env)
```

### 關閉與重新啟動

停止 minikube

```
$ minikube stop
Stopping local Kubernetes cluster...
Machine stopped.
```

確認狀態

```
$ minikube status
Stopped
```

啟動 minikube

```
$ minikube start
Starting local Kubernetes cluster...
Kubernetes is available at https://192.168.99.100:8443.
Kubectl is now configured to use the cluster.
```

如果想要使用特定版本的 Kubernetes，可以在啟動時用 `--kubernete-version` 參數指定

```
$ minikube start --kubernetes-version v1.7.3
```

### ssh 連線到 minikube 虛擬主機中

要連線到 minikube 主機時，可以使用 `minikube ssh` 的指令

![minikube ssh](assets/minikube-ssh.png)

## 使用 docker machine kvm driver 開 minikube

[裝了kvm](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#kvm-driver) 後，可以使用 docker machine driver kvm 來建立 minikube：

1.[準備 kvm driver](https://github.com/dhiltgen/docker-machine-kvm/releases)，以Linux Ubuntu 16.04 為例:

```
$ curl -o docker-machine-driver-kvm \
  -L https://github.com/dhiltgen/docker-machine-kvm/releases/download/v0.10.0/docker-machine-driver-kvm-ubuntu16.04 \
  && chmod +x docker-machine-driver-kvm \ 
  && sudo mv docker-machine-driver-kvm /usr/local/bin
```

2.啟動 minikube 或寫入 config

單次使用：

```
$ minikube start --vm-driver=kvm
```

寫入 config：

a. minikube config 預設路徑於 `~/.minikube/config/config.json`

加入 entity : `"vm-driver": "kvm" `

\[config.json\]

```
{
    "vm-driver": "kvm"
}
```

b. 或使用指令寫入 config

```
$ minikube config set vm-driver kvm
```

3.即可正常使用 `minikube start` 啟動。

### 參考文件

* minikube readme: [https://github.com/kubernetes/minikube/blob/master/README.md](https://github.com/kubernetes/minikube/blob/master/README.md)
* minikube release note: [https://github.com/kubernetes/minikube/releases](https://github.com/kubernetes/minikube/releases)



