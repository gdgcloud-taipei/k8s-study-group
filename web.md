# 一個完整的Web服務部署

本篇以一個完整的Web服務不屬為出發，在服務的部署上，將包含Service, Deployment與Pod幾個元素，以下圖來表示：

![Relation of Deploy, Pod and Service](/assets/relation-of-svc-pod-deploy.png)

上面的架構可以用底下的yaml檔案來描述：

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
  selector:
    app: myweb
    tier: myweb
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
        - name: NODE_ENV
          value: production
        - name: GET_HOSTS_FROM
          value: dns

```

其中上面描述檔的幾個重點：
* yaml檔中描述兩個不同的元素，可以用"---"分隔開來。
* 下半段為描述Deployment元素，其中該deploy包含一個container，並且以80 port對外服務
* 上半段為將Deployment以Service的形式公開對外服務(public)，且開啟80 port對應內部deployment的80 port提供服務。

透過這樣的部屬方式，提供了未來有需要時後可以直接scale out您的AP Server來乘載更大量request的優點，如下圖所示：

![Scaleout](/assets/scaleout.png)