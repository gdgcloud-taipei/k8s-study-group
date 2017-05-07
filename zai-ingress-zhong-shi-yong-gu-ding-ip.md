# 在Ingress中使用固定IP

目前筆者是在Google Container Engine(GKE)上設定Ingress... 在GKE上，需要先針對HTTP(S) Load Balancer指定一個Global IP，並且給他一個名稱。然後透過名稱的對應，設定該名稱到Ingress的metadata中... 

在Ingress中設定與Service稍有不同，設定上需要針對Ingress的metadata部分加上"kubernetes.io/ingress.global-static-ip-name"這組key，然後指定到已經book的靜態IP名稱即可。

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: myweb-ingress
  annotations:
    kubernetes.io/ingress.global-static-ip-name: "your-ip-name"
spec:
  backend:
    serviceName: myweb
    servicePort: 80
```

## 參考

* https://github.com/kelseyhightower/ingress-with-static-ip