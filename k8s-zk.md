```
kubectl apply -f https://raw.githubusercontent.com/7error/k8s-examples/master/yaml/k8s.yaml
kubectl get pod -o wide -lapp=zk
kubectl get svc -o wide -lapp=zk
kubectl logs zk-0
kubectl exec zk-1 -- sh -c 'cat /var/lib/zookeeper/data/myid'
kubectl exec zk-0 -- sh -c 'cat /opt/zookeeper/conf/zoo.cfg'

kubectl run -it --rm --image=busybox:1.28.3 busy -- /bin/sh -c "sleep 2s;echo conf | nc zk-0.zk-hs.default.svc.cluster.local 2181"



```

* <https://kubernetes.io/docs/tutorials/stateful-application/zookeeper/>