```bash
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/consul-cluster-statefulset.yaml

kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/consul-cluster-daemonset.yaml

kubectl get sts,svc,pod,cm,ep -l app=consul -o wide


kubectl run -it --rm --restart=Never --generator=run-pod/v1 --image=praqma/network-multitool pod-$RANDOM -- /bin/sh -c 'dig +noall +answer *.my-release-consul.default.svc.cluster.local'


kubectl -n default logs my-release-consul-0


kubectl -n default exec -it my-release-consul-0 -- /bin/sh -c "consul members"

kubectl -n default exec -it my-release-consul-0 -- /bin/sh -c "consul operator raft list-peers"

```

* https://github.com/kelseyhightower/consul-on-kubernetes
* https://github.com/bitnami/bitnami-docker-consul
