# 使用helm

```
gcloud --project=myproject container clusters \
  get-credentials my-cluster-name \
  --zone asia-east1-c
```

### Check Config
```
kubectl config current-context
```


### Repository update 

```
helm repo update
```


### Search package
```
helm search mysql
```

### Install package
```
$ helm install stable/mysql
NAME:   wishing-bear
LAST DEPLOYED: Wed Dec 21 11:08:21 2016
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/PersistentVolumeClaim
NAME                 STATUS    VOLUME    CAPACITY   ACCESSMODES   AGE
wishing-bear-mysql   Pending                                      0s

==> v1/Secret
NAME                 TYPE      DATA      AGE
wishing-bear-mysql   Opaque    2         0s
==> v1/Service
NAME                 CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
wishing-bear-mysql   10.63.244.122   <none>        3306/TCP   0s
==> extensions/Deployment
NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
wishing-bear-mysql   1         0         0            0           0s
NOTES:
MySQL can be accessed via port 3306 on the following DNS name from within your cluster:
wishing-bear-mysql.default.svc.cluster.local
To get your root password run:
    printf $(printf '\%o' `kubectl get secret --namespace default wishing-bear-mysql -o jsonpath="{.data.mysql-ro
ot-password[*]}"`);echo
To connect to your database:
1. Run an Ubuntu pod that you can use as a client:
    kubectl run -i --tty ubuntu --image=ubuntu:16.04 --restart=Never -- bash -il
2. Install the mysql client:
    $ apt-get update && apt-get install mysql-client -y
3. Connect using the mysql cli, then provide your password:
    $ mysql -h wishing-bear-mysql -p
```


### List helm installed packages

```
helm ls
```

### Delete 

```
helm delete wishing-bear-mysql
```