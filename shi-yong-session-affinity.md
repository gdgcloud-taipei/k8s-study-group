# 使用Session Affinity

K8S中的Service概念就像Load Balancer可以負載平衡前端的Request給背後的Pods，讓Pods可以提供回應... 但是當後端的Pod有多個的時候，就會遇到session在request導到不同Pods時候，Session遺失的問題。

為了解決這樣的問題，當然最完整的是使用AP層的Session工具(例如redis)來做到session共用... 但在簡單的情境下，我們可以透過session affinity的設定來盡量讓來自同個client的request可以由同一個Pod回覆，這樣就可以避免session遺失的問題...

## 設定

主要設定是在service上加上：

```
sessionAffinity: ClientIP
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
  sessionAffinity: ClientIP
  loadBalancerIP: 123.123.123.123
```

目前sessionAffinity可以提供"None"與"ClientIP"兩種設定：

* None: 以round robin的方式輪詢下面的Pods。
* ClientIP: 以client ip的方式固定request到同一台機器。