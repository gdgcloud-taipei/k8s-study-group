# 使用Deployment更新Image版本

在K8S的特色中，以rolling update這個feature最吸引我，但是rolling update偏偏只能用在replication controller中，而目前官方建議以deployment的部屬方式來取代replication controller，言下之意也就是deployment有可以取代replication controller的方式...

下面是一個簡單的部屬檔，包含一個deployment、一個service、一個ingress配置，其中service設定了sessionAffinity，且ingress設定了靜態IP位置...

```
apiVersion: v1
kind: Service
metadata:
  name: myweb
  labels:
    app: myweb
    tier: myweb
spec:
  ports:
  - port: 80
    targetPort: 80
  type: NodePort
  selector:
    app: myweb
    tier: myweb
  sessionAffinity: ClientIP
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: myweb
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: myweb
        tier: myweb
    spec:
      containers:
      - name: myweb
        image: peihsinsu/simpleweb
        ports:
        - containerPort: 80
        env:
        - name: PORT
          value: "80"
        - name: GET_HOSTS_FROM
          value: dns
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: myweb-ingress
  annotations:
    kubernetes.io/ingress.global-static-ip-name: "ip-myweb-server"
spec:
  backend:
    serviceName: myweb
    servicePort: 80
```

上面的設定檔案存成myweb.yaml，然後可以透過下面指令服務部署...

```
# kubectl create -f myweb.yaml
```

當image更新了新的版本後，在rolling update跟deployment的部屬方式來說，都建議要以image的tag來做命名...

如果這時候需要重新build您的image的話，可以透過docker build來取名，並增加上v2的tag作為識別

```
# docker build -t peihsinsu/simpleweb:v2 .
```

如果是已經build成peihsinsu/simpleweb的話，可以直接針對該image下個tag...

```
# docker tag peihsinsu/simpleweb peihsinsu/simpleweb:v2
```

此時，在不影響現有服務下，執行image更新最好的方式則是透過kubectl set image...設定新版本的image...

語法：

```
kubectl set image deploy/[deploy_name] [deploy_name]=[new_image_with_tag]
```

Ex:
```
# kubectl set image deploy/myweb myweb=peihsinsu/simpleweb:v2
```

如果一切無誤，則會看到新的instance啟動中，且現有的instance在關閉中... 慢慢的版本就會完全被置換完 :D