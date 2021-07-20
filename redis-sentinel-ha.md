```bash
kubectl apply -f https://raw.githubusercontent.com/7error/k8s-examples/master/yaml/redis-sentinel-ha.yaml

kubectl run -it --rm  --image=redis:5.0.6-alpine redis-$RANDOM -- sh

redis-cli -h demo-redis-ha -p 6379 info server
redis-cli -h demo-redis-ha -p 26379 INFO Sentinel
redis-cli -h demo-redis-ha -p 26379 SENTINEL masters
redis-cli -h demo-redis-ha -p 26379 SENTINEL slaves mymaster

kubectl get pod,svc,ep  -o wide -lapp=redis-ha -o wide
```

http://www.xwood.net/_site_domain_/_root/5870/5874/t_c279788.html
