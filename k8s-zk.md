```
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/k8s.yaml
kubectl get pod -o wide -lapp=zk
kubectl get svc -o wide -lapp=zk
kubectl logs zk-0
kubectl exec zk-1 -- sh -c 'cat /var/lib/zookeeper/data/myid'
kubectl exec zk-0 -- sh -c 'cat /opt/zookeeper/conf/zoo.cfg'

# ref https://kubernetes.io/docs/tutorials/stateful-application/zookeeper/

```
