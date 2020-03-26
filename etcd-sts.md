```
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/etcd-sts.yaml
kubectl get svc,pod,cm -lapp.kubernetes.io/name=etcd -lapp.kubernetes.io/instance=demo -o wide
kubectl logs demo-etcd-0
kubectl exec demo-etcd-0 -- etcdctl member list
```
