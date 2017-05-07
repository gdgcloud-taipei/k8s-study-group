# Service中使用固定IP

前文中提到建立一個Service並調用LoadBalancer的Yaml如下：

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-app
  labels:
    app: nginx-app
    tier: nginx-app
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: nginx-app
    tier: nginx-app
  type: LoadBalancer
```

如果希望在其上加上固定IP的設定，只要在最後指定loadBalancerIP即可

```
loadBalancerIP: your-ip-address
```

完整設定如下：

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-app
  labels:
    app: nginx-app
    tier: nginx-app
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: nginx-app
    tier: nginx-app
  type: LoadBalancer
  loadBalancerIP: 123.123.123.123
```




