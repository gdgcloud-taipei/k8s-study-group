# Scale out你的服務

在K8S上的scale out還滿簡單的，通常可以scale out的單位是用Deployment包裝起來的Pod，下面是個scale out的範例：

```
# kubectl get pods,svc,deploy
NAME                              READY     STATUS    RESTARTS   AGE
po/iot-couchdb-2476679917-hqv22   1/1       Running   0          58m
po/iot-redis-2940781838-p5qqq     1/1       Running   0          57m
po/iot-server-2844449461-7ljj4    1/1       Running   0          11m
po/service-loadbalancer-jzmgb     1/1       Running   1          19h

NAME              CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
svc/iot-couchdb   10.110.72.39   <none>        5984/TCP   58m
svc/iot-redis     10.98.159.71   <none>        6379/TCP   57m
svc/iot-server    10.101.151.5   10.244.3.18   3000/TCP   11m
svc/kubernetes    10.96.0.1      <none>        443/TCP    1d

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/iot-couchdb   1         1         1            1           58m
deploy/iot-redis     1         1         1            1           57m
deploy/iot-server    1         1         1            1           11m
```

假設我們希望iot-server可以scale到3台... 可以這樣操作：

```
# kubectl scale --replicas=3 deploy/iot-server
```

指令執行無誤的話，可以看到很快的，iot-server會出現三個replica來提供服務...

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

## Reference

* http://kubernetes.io/docs/user-guide/kubectl/kubectl_scale/