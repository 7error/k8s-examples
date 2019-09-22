
```bash
# simple use nodeSelector and hostPath
kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/redis-single.yaml

kubectl get cm,svc,ep,po -l app=redis -l release=release11


kubectl run -it --rm  --generator=run-pod/v1 --image=redis:5.0.5-alpine redis-$RANDOM -- /bin/sh

redis-cli -h release11-redis.default.svc -r 60 -i 1 info  | grep connected_clients
redis-cli -h release11-redis.default.svc --stat
redis-cli -h release11-redis.default.svc info
```
