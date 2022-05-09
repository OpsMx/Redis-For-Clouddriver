# Step by Step Instructions to Setup Redis for Clouddriver Services

1. Create Redis Statefulset by copying the content from the statefulset file [available here](https://github.com/OpsMx/Redis-For-Clouddriver/blob/main/clouddriver-redis-statefulset.yml)
2. Create Redis Service to point it to Clouddriver from the service file [available here](https://github.com/OpsMx/Redis-For-Clouddriver/blob/main/clouddriver-redis-service.yaml)

## Commands to Execute

1. Execute the below command to create the Redis yml files for Clouddriver.

    ```kubectl apply -f <statefulset.yml>  -n  <namespace>```
    
    ```kubectl apply -f <service.yml>  -n  <namespace>```
    
    ```kubectl apply -f <secret.yml>  -n  <namespace>```
    
    ```kubectl apply -f <health-cm.yml>  -n  <namespace>```
    
    ```kubectl apply -f <redis-cm.yml>  -n  <namespace>```

## Modifications to be done in the Halyard

#### Note: Instructions are provided with the assumption that your halyard is mounted on SCM(Github,GitLab,BitBucket,e.t.c)

- Navigate to "default/profiles" and create "clouddriver-<caching/ro/rw/ro-deck>-local.yml" files.

##### Note: If you have HA enabled for Clouddriver you will have create "clouddriver-<caching/ro/rw/ro-deck>-local.yml" if not only "clouddriver-local.yml"
```
Example: 
clouddriver-caching-local.yml
clouddriver-rw-local.yml
clouddriver-ro-deck-local.yml
clouddriver-ro-local.yml
```

- Update the below details in "clouddriver-<>-local.yml" files
```
services.redis.baseUrl: redis://:password@<service-name of new redis>:6379
redis.poll.timeoutSeconds: 600
redis.poll.intervalSeconds: 75
server.max-http-header-size: 100000
```
##### Note: Baseurl is based on the service what we have created

- Once, the above changes are done. Restart the halyard container.
- To check/confirm if Redis is connected with newly created Redis. Scale down the newly created, all clouddriver pods status will be changed until Redis is up and running again.
