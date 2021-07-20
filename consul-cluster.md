```bash
# apply server
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/consul-cluster-statefulset.yaml

kubectl get sts,svc,pod,cm,ep -l app=consul -o wide

# apply client
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/consul-cluster-daemonset.yaml

kubectl get ds,pod -l app=consul-ds -o wide


kubectl run -it --rm --restart=Never --image=praqma/network-multitool pod-$RANDOM -- /bin/sh -c 'dig +noall +answer *.my-release-consul.default.svc.cluster.local'


kubectl -n default logs my-release-consul-0

kubectl -n default exec -it my-release-consul-0 -- /bin/sh -c "consul operator raft list-peers"


Node           ID                                    Address             State     Voter  RaftProtocol
172.20.76.250  0461ce46-7372-87de-f517-9d251d0fc3ac  172.20.76.250:8300  leader    true   3
172.20.2.161   21272504-4c2d-ef41-2f46-ae94abef37fb  172.20.2.161:8300   follower  true   3
172.20.119.37  5486498d-dd56-7258-3002-249941224a24  172.20.119.37:8300  follower  true   3


kubectl -n default exec -it my-release-consul-0 -- /bin/sh -c "consul members"

Node                 Address              Status  Type    Build  Protocol  DC   Segment
172.20.119.37   172.20.119.37:8301   alive   server  1.6.1  2         dc1  <all>
172.20.2.161    172.20.2.161:8301    alive   server  1.6.1  2         dc1  <all>
172.20.76.250   172.20.76.250:8301   alive   server  1.6.1  2         dc1  <all>
172.20.119.38   172.20.119.38:8301   alive   client  1.6.1  2         dc1  <default>
172.20.123.138  172.20.123.138:8301  alive   client  1.6.1  2         dc1  <default>
172.20.2.162    172.20.2.162:8301    alive   client  1.6.1  2         dc1  <default>
172.20.76.252   172.20.76.252:8301   alive   client  1.6.1  2         dc1  <default>


```

* https://github.com/kelseyhightower/consul-on-kubernetes
* https://github.com/bitnami/bitnami-docker-consul
* https://hub.helm.sh/charts/bitnami/consul
* https://hub.docker.com/r/bitnami/consul
