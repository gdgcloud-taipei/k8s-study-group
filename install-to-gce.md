# 快速安裝K8S在GCE主機上


Kubernetes原始程式中，已經有包含GCE上的安裝環境，可以讓使用者透過簡單的指令來安裝GCE上的K8S Cluster群組。

## 事先準備

* 已經有Google Cloud Platform的專案，並且已經設定好
* 已經有安裝gcloud並且已經設定好相關認證權限

## 下載kubernetes的release包

下載位置：https://github.com/kubernetes/kubernetes/releases

以下載k8s 1.3.2的部分，例如：
```
# wget https://github.com/kubernetes/kubernetes/releases/download/v1.3.2/kubernetes.tar.gz && tar-xzf kubernetes.tar.gz
```

## 使用GCE provider來安裝

收先在解壓縮好的資料夾中，有個cluster的資料夾，切入後執行下面指令...

```
root@instance-1:~/kubernetes/cluster# ./kube-up.sh
... Starting cluster in us-central1-b using provider gce
... calling verify-prereqs


Your current Cloud SDK version is: 117.0.0
Installing components from version: 117.0.0

┌──────────────────────────────────────────────┐
│     These components will be installed.      │
├───────────────────────┬────────────┬─────────┤
│          Name         │  Version   │   Size  │
├───────────────────────┼────────────┼─────────┤
│ gcloud Alpha Commands │ 2016.01.12 │ < 1 MiB │
└───────────────────────┴────────────┴─────────┘

For the latest full release notes, please visit:
  https://cloud.google.com/sdk/release_notes

Do you want to continue (Y/n)?  Y

╔════════════════════════════════════════════════════════════╗
╠═ Creating update staging area                             ═╣
╠════════════════════════════════════════════════════════════╣
╠═ Installing: gcloud Alpha Commands                        ═╣
╠════════════════════════════════════════════════════════════╣
╠═ Creating backup and activating new installation          ═╣
╚════════════════════════════════════════════════════════════╝

Performing post processing steps...done.

Update done!



Your current Cloud SDK version is: 117.0.0
Installing components from version: 117.0.0

┌─────────────────────────────────────────────┐
│     These components will be installed.     │
├──────────────────────┬────────────┬─────────┤
│         Name         │  Version   │   Size  │
├──────────────────────┼────────────┼─────────┤
│ gcloud Beta Commands │ 2016.01.12 │ < 1 MiB │
└──────────────────────┴────────────┴─────────┘

For the latest full release notes, please visit:
  https://cloud.google.com/sdk/release_notes

Do you want to continue (Y/n)?  Y

╔════════════════════════════════════════════════════════════╗
╠═ Creating update staging area                             ═╣
╠════════════════════════════════════════════════════════════╣
╠═ Installing: gcloud Beta Commands                         ═╣
╠════════════════════════════════════════════════════════════╣
╠═ Creating backup and activating new installation          ═╣
╚════════════════════════════════════════════════════════════╝

Performing post processing steps...done.

Update done!


All components are up to date.
... calling kube-up
Your active configuration is: [default]

Project: sunny-573
Zone: us-central1-b
gs://kubernetes-staging-abdb20f570/kubernetes-devel/
+++ Staging server tars to Google Storage: gs://kubernetes-staging-abdb20f570/kubernetes-devel
+++ kubernetes-server-linux-amd64.tar.gz uploaded (sha1 = 2d6a2d1b59e27e7352afac30a0c1e2afb010063a)
+++ kubernetes-salt.tar.gz uploaded (sha1 = 4afb6014706d50a2a8baa9d1d5c781ff916e38a7)
+++ kubernetes-manifests.tar.gz uploaded (sha1 = 660033e7ce4a704a6463d170229f9e2183a4c2ef)
INSTANCE_GROUPS=
NODE_NAMES=
Looking for already existing resources
Starting master and configuring firewalls
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/zones/us-central1-b/disks/kubernetes-master-pd].
NAME                  ZONE           SIZE_GB  TYPE    STATUS
kubernetes-master-pd  us-central1-b  20       pd-ssd  READY
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/regions/us-central1/addresses/kubernetes-master-ip].
Generating certs for alternate-names: IP:104.154.234.221,IP:10.0.0.1,DNS:kubernetes,DNS:kubernetes.default,DNS:kubernetes.default.svc,DNS:kubernetes.default.svc.cluster.local,DNS:kubernetes-master
+++ Logging using Fluentd to gcp
WARNING: You have selected a disk size of under [200GB]. This may result in poor I/O performance. For more information, see: https://developers.google.com/compute/docs/disks#pdperformance.
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/global/firewalls/default-default-internal].
NAME                      NETWORK  SRC_RANGES  RULES                         SRC_TAGS  TARGET_TAGS
default-default-internal  default  10.0.0.0/8  tcp:1-65535,udp:1-65535,icmp
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/global/firewalls/kubernetes-master-https].
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/global/firewalls/default-default-ssh].
NAME                     NETWORK  SRC_RANGES  RULES    SRC_TAGS  TARGET_TAGS
kubernetes-master-https  default  0.0.0.0/0   tcp:443            kubernetes-master
NAME                 NETWORK  SRC_RANGES  RULES   SRC_TAGS  TARGET_TAGS
default-default-ssh  default  0.0.0.0/0   tcp:22
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/zones/us-central1-b/instances/kubernetes-master].
NAME               ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP      STATUS
kubernetes-master  us-central1-b  n1-standard-1               10.240.0.13  104.154.234.221  RUNNING
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/global/firewalls/kubernetes-minion-all].
NAME                   NETWORK  SRC_RANGES     RULES                     SRC_TAGS  TARGET_TAGS
kubernetes-minion-all  default  10.244.0.0/14  tcp,udp,icmp,esp,ah,sctp            kubernetes-minion
Creating minions.
Attempt 1 to create kubernetes-minion-template
WARNING: You have selected a disk size of under [200GB]. This may result in poor I/O performance. For more information, see: https://developers.google.com/compute/docs/disks#pdperformance.
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/global/instanceTemplates/kubernetes-minion-template].
NAME                        MACHINE_TYPE   PREEMPTIBLE  CREATION_TIMESTAMP
kubernetes-minion-template  n1-standard-2               2016-07-17T19:51:29.523-07:00
Created [https://www.googleapis.com/compute/v1/projects/sunny-573/zones/us-central1-b/instanceGroupManagers/kubernetes-minion-group].
NAME                     LOCATION       SCOPE  BASE_INSTANCE_NAME       SIZE  TARGET_SIZE  INSTANCE_TEMPLATE           AUTOSCALED
kubernetes-minion-group  us-central1-b  zone   kubernetes-minion-group  0     3            kubernetes-minion-template  no
Waiting for group to become stable, current operations: creating: 3
Waiting for group to become stable, current operations: creating: 3
Waiting for group to become stable, current operations: creating: 3
Waiting for group to become stable, current operations: creating: 3
Group is stable
INSTANCE_GROUPS=kubernetes-minion-group
NODE_NAMES=kubernetes-minion-group-a4bx kubernetes-minion-group-fhxe kubernetes-minion-group-h0w4
Using master: kubernetes-master (external IP: 104.154.234.221)
Waiting up to 300 seconds for cluster initialization.

  This will continually check to see if the API for kubernetes is reachable.
  This may time out if there was some uncaught error during start up.

..................Kubernetes cluster created.
cluster "sunny-573_kubernetes" set.
user "sunny-573_kubernetes" set.
context "sunny-573_kubernetes" set.
switched to context "sunny-573_kubernetes".
user "sunny-573_kubernetes-basic-auth" set.
Wrote config for sunny-573_kubernetes to /root/.kube/config

Kubernetes cluster is running.  The master is running at:

  https://104.154.234.221

The user name and password to use is located in /root/.kube/config.

... calling validate-cluster
Waiting for 4 ready nodes. 1 ready nodes, 4 registered. Retrying.
Waiting for 4 ready nodes. 1 ready nodes, 4 registered. Retrying.
Found 4 node(s).
NAME                           STATUS                     AGE
kubernetes-master              Ready,SchedulingDisabled   44s
kubernetes-minion-group-a4bx   Ready                      46s
kubernetes-minion-group-fhxe   Ready                      44s
kubernetes-minion-group-h0w4   Ready                      46s
Validate output:
NAME                 STATUS    MESSAGE              ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-0               Healthy   {"health": "true"}
etcd-1               Healthy   {"health": "true"}
Cluster validation succeeded
Done, listing cluster services:

Kubernetes master is running at https://104.154.234.221
GLBCDefaultBackend is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/default-http-backend
Heapster is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/heapster
KubeDNS is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/kube-dns
kubernetes-dashboard is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard
Grafana is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/monitoring-grafana
InfluxDB is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/monitoring-influxdb

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```


## 查詢cluster的金鑰與帳號密碼

```
# cat /root/.kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJ...(很長...忽略)S0K
    server: https://104.154.234.221
  name: sunny-573_kubernetes
contexts:
- context:
    cluster: sunny-573_kubernetes
    user: sunny-573_kubernetes
  name: sunny-573_kubernetes
current-context: sunny-573_kubernetes
kind: Config
preferences: {}
users:
- name: sunny-573_kubernetes
  user:
    client-certificate-data: Q2VydGl...(很長...忽略)S0tCg==
    token: nu...yWY8 (秘密，不給知道)
- name: sunny-573_kubernetes-basic-auth
  user:
    password: b....7 (秘密，不給知道)
    username: admin
```

有了最後basic auth的帳號密碼之後，就可以去找k8s的dashboard做登入，至於怎麼找到dashboard的位置... 除了剛剛裝好時候有提示，也可以使用下面指令列出dashboard的位置...

```
# kubectl cluster-info
Kubernetes master is running at https://104.154.234.221
GLBCDefaultBackend is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/default-http-backend
Heapster is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/heapster
KubeDNS is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/kube-dns
kubernetes-dashboard is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard
Grafana is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/monitoring-grafana
InfluxDB is running at https://104.154.234.221/api/v1/proxy/namespaces/kube-system/services/monitoring-influxdb
```

在open source版本的k8s上，是透過grafana與influxdb來做監控資料的搜集與呈現，上面最後兩行是他們的位置... 另外也可以看到kubernetes-dashboard的位置，我們可以透過瀏覽器瀏覽這個位置，然後輸入上面所取得的admin的密碼，即可連線操作！

## 刪除測試資源

在完成測試後，可以在同資料夾內，透過shutdown script來做清除的動作...

```
root@instance-1:~/kubernetes/cluster# ./kube-down.sh
Bringing down cluster using provider: gce

All components are up to date.

All components are up to date.

All components are up to date.
Your active configuration is: [default]

Project: sunny-573
Zone: us-central1-b
INSTANCE_GROUPS=kubernetes-minion-group
NODE_NAMES=kubernetes-minion-group-a4bx kubernetes-minion-group-fhxe kubernetes-minion-group-h0w4
Bringing down cluster
Deleted [https://www.googleapis.com/compute/v1/projects/sunny-573/zones/us-central1-b/instanceGroupManagers/kubernetes-minion-group].
Deleted [https://www.googleapis.com/compute/v1/projects/sunny-573/global/instanceTemplates/kubernetes-minion-template].
Updated [https://www.googleapis.com/compute/v1/projects/sunny-573/zones/us-central1-b/instances/kubernetes-master].
Deleted [https://www.googleapis.com/compute/v1/projects/sunny-573/zones/us-central1-b/instances/kubernetes-master].
Deleted [https://www.googleapis.com/compute/v1/projects/sunny-573/global/firewalls/kubernetes-master-https].
Deleted [https://www.googleapis.com/compute/v1/projects/sunny-573/global/firewalls/kubernetes-minion-all].
Deleting routes kubernetes-c186b3ae-4c92-11e6-8f8d-42010af0000d
Deleted [https://www.googleapis.com/compute/v1/projects/sunny-573/global/routes/kubernetes-c186b3ae-4c92-11e6-8f8d-42010af0000d].
Deleted [https://www.googleapis.com/compute/v1/projects/sunny-573/regions/us-central1/addresses/kubernetes-master-ip].
property "clusters.sunny-573_kubernetes" unset.
property "users.sunny-573_kubernetes" unset.
property "users.sunny-573_kubernetes-basic-auth" unset.
property "contexts.sunny-573_kubernetes" unset.
property "current-context" unset.
Cleared config for sunny-573_kubernetes from /root/.kube/config
Done
```

## 參考

* 本篇文章參考自：http://kubernetes.io/docs/getting-started-guides/gce/
