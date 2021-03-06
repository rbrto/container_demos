# Hello world
https://github.com/dapr/samples/tree/master/2.hello-kubernetes

## redis
https://github.com/dapr/docs/blob/master/concepts/components/redis.md#creating-a-redis-store
https://github.com/dapr/docs/blob/master/concepts/components/redis.md#creating-a-redis-cache-in-your-kubernetes-cluster-using-helm

helm install stable/redis --name redis --set password=secretpassword 
helm install redis stable/redis --set password=secretpassword 
helm delete redis --purge

kubectl get secret --namespace default redis -o jsonpath="{.data.redis-password}" | base64 --decode 

cat <<EOF | kubectl apply -f -
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: messagebus
spec:
  type: pubsub.redis
  metadata:
  - name: redisHost
    value: redis-master:6379
  - name: redisPassword
    value: secretpassword
EOF

cat <<EOF | kubectl apply -f -
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  metadata:
  - name: redisHost
    value: redis-master:6379
  - name: redisPassword
    value: secretpassword
EOF

REDIS_HOST=.redis.cache.windows.net:6379
REDIS_PASSWORD=

cat <<EOF | kubectl apply -f -
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  metadata:
  - name: redisHost
    value: $REDIS_HOST
  - name: redisPassword
    value: $REDIS_PASSWORD
EOF

