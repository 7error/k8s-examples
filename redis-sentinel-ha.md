kubectl apply -f https://raw.githubusercontent.com/7error/watch/master/redis-sentinel-ha.yaml

kubectl run -it --rm  --generator=run-pod/v1 --image=redis:5.0.6-alpine redis-$RANDOM -- sh

redis-cli -h demo-redis-ha -p 6379 info server
redis-cli -h demo-redis-ha -p 26379 INFO Sentinel
redis-cli -h demo-redis-ha -p 26379 SENTINEL masters
redis-cli -h demo-redis-ha -p 26379 SENTINEL slaves mymaster

kubectl get pod,svc,ep  -o wide -lapp=redis-ha -o wide
