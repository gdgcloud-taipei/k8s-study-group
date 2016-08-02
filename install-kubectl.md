# 安裝kubectl


安裝kubectl目前有兩個常用的安裝方式：

* 從release的package下載
* 直接下載各平台已經complie好的套件

## 從release的package下載

請直接到github的release位置：https://github.com/kubernetes/kubernetes/releases 下載適合的版本，然後再解壓縮...

```
wget https://github.com/kubernetes/kubernetes/releases/download/v1.3.4/kubernetes.tar.gz && \
tar -xzf kubernetes.tar.gz
```

以linux x64主機而言，切換到這個位置：$kubernetes-unpackage-path/platforms/linux/amd64
即可在下面發現可執行的kubectl檔案...

接著把該位置的kubectl直接複製到/usr/bin下面，並增加執行權限...

```
cp kubectl /usr/bin/kubectl && chmod u+x /usr/bin/kubectl
```

接著就可以測試看看拉～

```
kubectl cluster-info
```

## 下載各平台已經complie好的套件

下面是官方提供的各種不同平台的kubectl的下載位置

* linux/amd64: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/linux/amd64/kubectl
* linux/386: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/linux/386/kubectl
* linux/arm: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/linux/arm/kubectl
* linux/arm64: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/linux/arm64/kubectl
* linux/ppc64le: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/linux/ppc64le/kubectl
* OS X/amd64: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/darwin/amd64/kubectl
* OS X/386: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/darwin/386/kubectl
* windows/amd64: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/windows/amd64/kubectl.exe
* windows/386: http://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/windows/386/kubectl.exe

以Linux為例，直接下載該位置的執行檔，並且存到/usr/bin下面，再賦予執行權限即可...

```
curl -sSL "https://storage.googleapis.com/kubernetes-release/release/v1.3.0/bin/linux/amd64/kubectl" > /usr/bin/kubectl
chmod +x /usr/bin/kubectl
```

接著就可以測試看看拉～

```
kubectl cluster-info
```

## 參考

* 官方網站 - 從release的tar執行：http://kubernetes.io/docs/user-guide/prereqs/
* 官方網站 - 各平台安裝好的套件位置：http://kubernetes.io/docs/getting-started-guides/docker/

