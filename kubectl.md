# 確認kubectl目前操作叢集資訊

當k8s操作集群有一個以上的時候，常常會發現記不得目前的操作對象，此時可以透過kubectl config來查詢：

```
# kubectl config current-context
gke_sunny-573_asia-east1-b_iot-test-cluster
```

如果需要知道更詳細的資訊，可以用get-contexts來確認一下cluster的進一步資訊...

```
# kubectl config get-contexts gke_sunny-573_asia-east1-b_iot-test-cluster
CURRENT   NAME                                          CLUSTER                                       AUTHINFO                                      NAMESPACE
*         gke_sunny-573_asia-east1-b_iot-test-cluster   gke_sunny-573_asia-east1-b_iot-test-cluster   gke_sunny-573_asia-east1-b_iot-test-cluster
```

如果有需要切換context，可以使用use-context來作切換:

```
# kubectl config use-context gke_sunny-573_asia-east1-b_iot-test-cluster
```

