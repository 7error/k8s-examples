```
kubectl apply -f https://raw.githubusercontent.com/7error/k8s-examples/master/yaml/etcd-sts.yaml
kubectl get svc,pod,cm -lapp.kubernetes.io/name=etcd -lapp.kubernetes.io/instance=demo -o wide
kubectl logs demo-etcd-0
kubectl exec demo-etcd-0 -- etcdctl member list

kubectl exec  demo-etcd-0 -- etcdctl endpoint status --endpoints "demo-etcd-0.demo-etcd-headless:2380,demo-etcd-1.demo-etcd-headless:2380,demo-etcd-2.demo-etcd-headless:2380"
demo-etcd-0.demo-etcd-headless:2380, d29d979f6dd51358, 3.4.5, 20 kB, true, false, 5, 9, 9,
demo-etcd-1.demo-etcd-headless:2380, c4bf70f7b1f304fd, 3.4.5, 20 kB, false, false, 5, 9, 9,
demo-etcd-2.demo-etcd-headless:2380, ff1e4c3bafe5fc79, 3.4.5, 20 kB, false, false, 5, 9, 9,

kubectl exec  demo-etcd-0 -- etcdctl endpoint health --endpoints "demo-etcd-0.demo-etcd-headless:2380,demo-etcd-1.demo-etcd-headless:2380,demo-etcd-2.demo-etcd-headless:2380"
demo-etcd-0.demo-etcd-headless:2380 is healthy: successfully committed proposal: took = 6.325774ms
demo-etcd-2.demo-etcd-headless:2380 is healthy: successfully committed proposal: took = 7.500852ms
demo-etcd-1.demo-etcd-headless:2380 is healthy: successfully committed proposal: took = 9.54835ms

```
