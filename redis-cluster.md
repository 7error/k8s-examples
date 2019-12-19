```bash
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/redis-cluster.yaml

kubectl get sts,pod,svc -o wide -l app=redis-cluster-ss

kubectl run -it --rm --restart=Never --generator=run-pod/v1 \
--image=dotbalo/redis-trib:4.0.10 pod-$RANDOM \
-- redis-trib.rb create --replicas 1 $(kubectl get pod -l app=redis-cluster-ss -o json | jq -r '[(.items[].status.podIP)+":6379"] | join(" ")')


kubectl exec -it redis-cluster-ss-0 -n default -- redis-cli cluster nodes

kubectl exec -it redis-cluster-ss-0 -n default -- redis-cli cluster info


kubectl run -it --rm --restart=Never --generator=run-pod/v1 \
--image=7error/kkkbox pod-$RANDOM \
-- /bin/sh -c 'sleep 3s; \
dig +noall +answer *.redis-cluster-ss.default.svc.cluster.local \
&& nping --tcp-connect -c1 -H -p 6379 redis-cluster-ss-0.redis-cluster-ss.default.svc.cluster.local';
```


* https://github.com/dotbalo/k8s
* https://hub.helm.sh/charts/stable/redis
* https://adolphor.com/blog/2017/03/23/redis-cluster-node-manager.html
