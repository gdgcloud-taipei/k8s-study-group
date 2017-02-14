# 使用namespace分隔服務

如果在一個kubernetes cluster上需要部署許多服務時候，namespace是一個將服務分隔的好選擇，透過不同的namespace，可以帶來許多管理上的優勢，例如：

1. 不同的namespace下的service name等可以重複，在多租戶(multi-tanent)的情況下，可以大量降低架構設計上的複雜度。
2. 不同的namespace的pod彼此之間無法內部連線，換句話說，不同namespace間是彼此隔離，不會危害到其他的namespace下的服務。
3. 當服務大量部署時，沒有namespace隔開，連列表都有點難找到自己要看的服務...@@

```
apiVersion: v1
kind: Namespace
metadata:
  name: myweb
  labels:
    name: myweb
```