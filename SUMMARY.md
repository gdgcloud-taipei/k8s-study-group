# Summary

* [簡介](README.md)
  * [Kubernetes概念](kubernetes_concept.md)
* [從安裝說起](installation.md)
  * [單機版K8S - minikube](minikube.md)
  * 單機版 K8S - docker for desktop
  * [使用docker-compose安裝kubernetes](install-from-docker-compose.md)
  * [使用 kubeadm 來建立 Kubernetes Cluster](kubeadm_kubernetes_cluster.md)
  * [使用 terraform 來建立 kubernetes cluster](shi-yong-terraform-lai-jian-li-kubernetes-cluster.md)
  * 使用 Kubespray 來建立 cluster
  * [快速安裝K8S在GCE主機上](install-to-gce.md)
  * [使用hyperkube image安裝K8S](use-hyperkube-image-install.md)
  * [使用kube-deploy安裝multi-node cluster](use-kube-deploy-install-multi-node-cluster.md)
  * [安裝kubectl](install-kubectl.md)
* [基本操作](basic-operation.md)
  * [查詢kubectl的資源](kubectl_get_xxx.md)
  * [建立nginx http server](run_nginx_http_server.md)
  * [建立一個具有 TLS 的 nginx 連線](secure_monolith.md)
  * [K8S的部屬結構](k8sde-bu-shu-jie-gou.md)
  * ConfigMap 的使用
* [進階操作](advance-operation.md)
  * Pod
    * [使用YAML描述部署Pod](deploy_using_yaml.md)
    * [Pod設定檔詳細說明](pod_config_detail_description.md)
    * [探討Pod中有多個container之狀況](pod_contains_multi_container.md)
    * [幫Pod增加Persistence Volume](add_persistence_volume.md)
    * [查詢log](check_pod_log.md)
    * [連線到Pod檢視內部](connect_to_pod_internal.md)
  * [Secret](secret.md)
    * [使用secret功能](using_secret.md)
  * Deployment
    * [使用Deployment部署](use_deployment.md)
    * [一個完整的Web服務部署](web.md)
    * [從Deployment更新Image版本](rolling_update_deployment.md)
  * Service
    * [使用Service為服務增加外部IP](use_service_as_external_access_point.md)
    * [Service中使用固定IP](use_static_ip.md)
    * [使用Session Affinity](shi-yong-session-affinity.md)
  * Scaling
    * [在Deployment部署下Scaleout](scaleout_deployment.md)
  * Ingress
    * [使用Ingress連結Layer7 Load Balancer](use_ingress.md)
    * [在Ingress中使用固定IP](zai-ingress-zhong-shi-yong-gu-ding-ip.md)
    * [建立SSL Ingress](jian-li-tls.md)
    * Ingress with Multi domain
  * [檢視資源使用狀態](k8s-states.md)
  * [確認kubectl目前操作叢集資訊](kubectl.md)
* K8S套件管理工具
  * [helm](helm.md)
* Kubernetes monitoring
  * [Prometheus](kubernetes-monitoring/prometheus.md)
* Kubeconfig management
  * [vim 也可以管理 Kubernetes](vim-ye-ke-yi-guan-li-kubernetes.md)
  * [結合 fish shell script 管理 kubernetes](jie-he-fish-shell-script-guan-li-kubernetes.md)
* [Kubernetes Log](kubernetes-log.md)
  * [使用 ELK 收集 Kubernetes Logs](shi-yong-elk-shou-ji-kubernetes-logs.md)
* [Security](security.md)
  * [Kubernetes Vault](security/kubernetes-vault.md)



