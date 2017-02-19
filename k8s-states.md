# 檢視資源使用狀態

在kubernetes中，提供了一個類似top的指令：kubectl top，可以讓使用者檢視目前集群的node與pod的狀態(cpu, memory)...

檢視各node的資源狀態

```
$ kubectl top nodes
NAME                                  CPU(cores)   CPU%      MEMORY(bytes)   MEMORY%
gke-imsg-default-pool-5c92dc53-jcw8   315m         7%        5393Mi          35%
gke-imsg-default-pool-5c92dc53-rryy   299m         7%        6267Mi          41%
```

檢視pod的資源狀態

```
$ kubectl top pods
NAME                    CPU(cores)   MEMORY(bytes)
testroom1480784781723   9m           120Mi
testroom1480784572734   9m           122Mi
testroom1480685799983   8m           121Mi
```

如果pod有掛載在其他的namespace之下，需要加上namespace來查詢...

```
$ kubectl top pod --namespace myweb
NAME                       CPU(cores)   MEMORY(bytes)
my-redis-894746158-njjzo   0m           25Mi
```

