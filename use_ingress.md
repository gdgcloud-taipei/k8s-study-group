# 使用Ingress連結Layer 7 Load Balancer

Ingress是設定讓K8S可以調用Load Balancer的方式，目前在GKE上已經結合Layer7 Load Balancer來提供服務，在開立的過程中，kubectl流程會包含打通firewall, instance group, instance template等等的動作..

一個簡單的ingress描述如下：

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: myweb-ingress
spec:
  backend:
    serviceName: myweb
    servicePort: 80

```

只要backend中的service對應無誤，則服務會以80 port提供服務。