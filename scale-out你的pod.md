# Scale out你的服務

Scale out的概念是當您的服務器資源無法應付連線數量或是運算量時候，維運人員會透過橫向擴展應用程式伺服器(AP Server)的數量來提高機器的可乘載數，讓服務得以繼續經營...

![Scaleout](/assets/scenario-iot-server.png)

在K8S上的scale out還滿簡單的，通常可以scale out的單位是用Deployment包裝起來的Pod，下面是個簡單的scale out的範例，包含兩個deployment(iot-server與iot-couchdb)，其中個包含一個running pod，運作狀態如下：

```
# kubectl get pods,svc,deploy
NAME                              READY     STATUS    RESTARTS   AGE
po/iot-couchdb-2476679917-hqv22   1/1       Running   0          58m
po/iot-server-2844449461-7ljj4    1/1       Running   0          11m
po/service-loadbalancer-jzmgb     1/1       Running   1          19h

NAME              CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
svc/iot-couchdb   10.110.72.39   <none>        5984/TCP   58m
svc/iot-server    10.101.151.5   10.244.3.18   3000/TCP   11m
svc/kubernetes    10.96.0.1      <none>        443/TCP    1d

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/iot-couchdb   1         1         1            1           58m
deploy/iot-server    1         1         1            1           11m
```

上面狀態所顯示，iot-server (po/iot-server-2844449461-7ljj4)在pod上的數量為一台，假設我們希望iot-server可以scale到3台... 可以這樣操作：

```
# kubectl scale --replicas=3 deploy/iot-server
```

指令執行無誤的話，可以看到很快的，iot-server會出現三個replica來提供服務...

```
# kubectl get pods,svc,deploy
NAME                              READY     STATUS    RESTARTS   AGE
po/iot-couchdb-2476679917-hqv22   1/1       Running   0          58m
po/iot-server-2844449461-7ljj4    1/1       Running   0          11m
po/iot-server-2844449461-srj10    1/1       Running   0          10m
po/iot-server-2844449461-z6ndg    1/1       Running   0          10m
po/service-loadbalancer-jzmgb     1/1       Running   1          19h

NAME              CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
svc/iot-couchdb   10.110.72.39   <none>        5984/TCP   58m
svc/iot-server    10.101.151.5   10.244.3.18   3000/TCP   11m
svc/kubernetes    10.96.0.1      <none>        443/TCP    1d

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/iot-couchdb   1         1         1            1           58m
deploy/iot-server    3         3         3            3           11m
```
承上操作，iot-server本身為透過deploy的方式封裝，因此在scale時，是直接針對該deploy的replica數量擴增，且deploy前面有service作為服務入口，因此整個服務在擴展成3台後，可以直接由這三台iot-server平均乘載外部的request...

如了手動scale out呢的福物外，新版的K8S也開始支援Auto Scale了喔，這部分容後跟大家介紹。

## Reference

* http://kubernetes.io/docs/user-guide/kubectl/kubectl_scale/