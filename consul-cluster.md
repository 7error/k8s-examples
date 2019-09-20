```bash
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/consul-cluster-statefulset.yaml

kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/consul-cluster-daemonset.yaml

kubectl get sts,svc,pod,cm,ep -l app=consul -o wide


kubectl run -it --rm --restart=Never --generator=run-pod/v1 --image=praqma/network-multitool pod-$RANDOM -- /bin/sh -c 'dig +noall +answer *.my-release-consul.default.svc.cluster.local'


kubectl -n default logs my-release-consul-0


kubectl -n default exec -it my-release-consul-0 -- /bin/sh -c "consul members"

Node                 ID                                    Address             State     Voter  RaftProtocol
my-release-consul-0  2bd192ac-c3a3-6207-6844-b7aa2b3edf1d  172.20.76.245:8300  follower  true   3
my-release-consul-1  bfc8bbc9-e32a-016b-e893-dc1ff1c3c807  172.20.2.149:8300   follower  true   3
my-release-consul-2  7cdba994-c0fd-f0e8-d912-78d84ca66f94  172.20.119.24:8300  leader    true   3


kubectl -n default exec -it my-release-consul-0 -- /bin/sh -c "consul operator raft list-peers"

Node                 Address              Status  Type    Build  Protocol  DC   Segment
my-release-consul-0  172.20.76.245:8301   alive   server  1.6.0  2         dc1  <all>
my-release-consul-1  172.20.2.149:8301    alive   server  1.6.0  2         dc1  <all>
my-release-consul-2  172.20.119.24:8301   alive   server  1.6.0  2         dc1  <all>
172.20.119.34        172.20.119.34:8301   alive   client  1.6.1  2         dc1  <default>
172.20.123.137       172.20.123.137:8301  alive   client  1.6.1  2         dc1  <default>
172.20.2.159         172.20.2.159:8301    alive   client  1.6.1  2         dc1  <default>
172.20.76.234        172.20.76.234:8301   alive   client  1.6.1  2         dc1  <default>


```

* https://github.com/kelseyhightower/consul-on-kubernetes
* https://github.com/bitnami/bitnami-docker-consul
