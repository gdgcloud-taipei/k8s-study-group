# 查詢kubectl的資源

使用kubectl get可以針對k8s上的資源進行查詢的動作，而目前\(k8s 1.3\)的版本可以支援的查詢如下：

* pods \(po\)
* services \(svc\)
* deployments
* replicasets \(rs\)
* replicationcontrollers \(rc\)
* nodes \(no\)
* events \(ev\)
* limitranges \(limits\)
* persistentvolumes \(pv\)
* persistentvolumeclaims \(pvc\)
* resourcequotas \(quota\)
* namespaces \(ns\)
* serviceaccounts \(sa\)
* ingresses \(ing\)
* horizontalpodautoscalers \(hpa\)
* daemonsets \(ds\)
* configmaps
* componentstatuses \(cs\)
* endpoints \(ep\)
* secrets

這些資源有不少有簡寫\(括弧裡面的\)可以使用，常打的不妨記一下... :D

如果嫌太多，不好記，可以直接按enter來查詢來查詢help page...

```
kubectl get
```



## 常用的查詢指令

### kubectl get一次取多個type

kubectl get支援透過","隔開來一次取多個不同的type的值...

```
# kubectl get pods,svc,deploy
NAME                              READY     STATUS    RESTARTS   AGE
po/iot-couchdb-2476679917-hqv22   1/1       Running   0          58m
po/iot-redis-2940781838-p5qqq     1/1       Running   0          57m
po/iot-server-2844449461-7ljj4    1/1       Running   0          11m
po/iot-server-2844449461-srj10    1/1       Running   0          10m
po/iot-server-2844449461-z6ndg    1/1       Running   0          10m
po/service-loadbalancer-jzmgb     1/1       Running   1          19h

NAME              CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
svc/iot-couchdb   10.110.72.39   <none>        5984/TCP   58m
svc/iot-redis     10.98.159.71   <none>        6379/TCP   57m
svc/iot-server    10.101.151.5   10.244.3.18   3000/TCP   11m
svc/kubernetes    10.96.0.1      <none>        443/TCP    1d

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/iot-couchdb   1         1         1            1           58m
deploy/iot-redis     1         1         1            1           57m
deploy/iot-server    3         3         3            3           11m
```

### 透過wide來延展欄位

kubectl get可以透過"-o wide"來擴充顯示的欄位，會多出

```
# kubectl get pods,svc,deploy -o wide
NAME                              READY     STATUS    RESTARTS   AGE       IP            NODE
po/iot-couchdb-2476679917-hqv22   1/1       Running   0          58m       10.244.3.20   k8s-n3
po/iot-redis-2940781838-p5qqq     1/1       Running   0          57m       10.244.2.27   k8s-n2
po/iot-server-2844449461-7ljj4    1/1       Running   0          11m       10.244.3.23   k8s-n3
po/iot-server-2844449461-srj10    1/1       Running   0          10m       10.244.1.31   k8s-n1
po/iot-server-2844449461-z6ndg    1/1       Running   0          10m       10.244.2.30   k8s-n2
po/service-loadbalancer-jzmgb     1/1       Running   1          19h       10.244.3.18   k8s-n3

NAME              CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE       SELECTOR
svc/iot-couchdb   10.110.72.39   <none>        5984/TCP   58m       app=couchdb,tier=iot-couchdb
svc/iot-redis     10.98.159.71   <none>        6379/TCP   57m       app=redis,tier=iot-redis
svc/iot-server    10.101.151.5   10.244.3.18   3000/TCP   11m       app=iot-server,tier=iot-server
svc/kubernetes    10.96.0.1      <none>        443/TCP    1d        <none>

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/iot-couchdb   1         1         1            1           58m
deploy/iot-redis     1         1         1            1           57m
deploy/iot-server    3         3         3            3           11m
```



