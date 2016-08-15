# 查詢kubectl的資源

使用kubectl get可以針對k8s上的資源進行查詢的動作，而目前(k8s 1.3)的版本可以支援的查詢如下：

* pods (po)
* services (svc)
* deployments
* replicasets (rs)
* replicationcontrollers (rc)
* nodes (no)
* events (ev)
* limitranges (limits)
* persistentvolumes (pv)
* persistentvolumeclaims (pvc)
* resourcequotas (quota)
* namespaces (ns)
* serviceaccounts (sa)
* ingresses (ing)
* horizontalpodautoscalers (hpa)
* daemonsets (ds)
* configmaps
* componentstatuses (cs)
* endpoints (ep)
* secrets

其中，可以使用

```
kubectl get
```

來查詢help page

