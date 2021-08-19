
```bash
# simple use nodeSelector and hostPath
kubectl apply -f https://raw.githubusercontent.com/7error/k8s-examples/master/yaml/redis-single.yaml

kubectl get cm,svc,ep,po -l app.kubernetes.io/name=redis -l app.kubernetes.io/instance=release11


kubectl run -it --rm  --image=redis:6.2-alpine redis-$RANDOM -- /bin/sh

redis-cli -h release11-redis-master.default.svc -r 60 -i 1 info  | grep connected_clients
redis-cli -h release11-redis-master.default.svc --stat
redis-cli -h release11-redis-master.default.svc info
```
