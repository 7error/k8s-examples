
```bash
# simple use nodeSelector and hostPath
kubectl apply -f https://raw.githubusercontent.com/7error/k8s-examples/master/yaml/redis-single.yaml

or

curl -fsSL https://raw.githubusercontent.com/7error/k8s-examples/master/yaml/redis-single.yaml \
| sed -e "s/n32.dev/yournode/g" \
| sed -e "s/release11/my-release/g" \
| kubectl apply -f -

kubectl get cm,svc,ep,po -l app=redis -l release=release11


kubectl run -it --rm  --image=redis:5.0.5-alpine redis-$RANDOM -- /bin/sh

redis-cli -h release11-redis.default.svc -r 60 -i 1 info  | grep connected_clients
redis-cli -h release11-redis.default.svc --stat
redis-cli -h release11-redis.default.svc info
```
