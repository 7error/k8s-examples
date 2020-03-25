```
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/k8s.yaml
kubectl get pod -o wide -lapp=zk
kubectl get svc -o wide -lapp=zk
kubectl logs zk-0
kubectl exec -it zk-1 -- sh -c 'cat /var/lib/zookeeper/data/myid'
```
