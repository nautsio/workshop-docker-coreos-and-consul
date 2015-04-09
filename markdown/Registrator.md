## Registrator

!SUB
### Features
* Service Registry Bridge for Docker
* subscribes to the Docker Event stream
* automatically registers instances as service
* to Consul, SkyDNS, etcd

https://github.com/gliderlabs/registrator

!SUB
### Service registration events
* docker instance starts -> register
* docker instance dies -> unregister

!SUB
### Service registrations
Default registers a service name for each port

```
docker run -d --name redis.0 -p 10000:6379 dockerfile/redis
```
results in an service entry
```
{
    "ID": "hostname:redis.0:6379",
    "Name": "redis",
    "Port": 10000,
    "IP": "192.168.1.102",
    "Tags": [],
    "Attrs": {}
}
```

!SUB
### Service registrations
Explicit service naming is possible

```
docker run -d --name mywebapp.0 \
	--env SERVICE_80_NAME=mywebapp \
	--env SERVICE_80_TAGS=http \
	--env SERVICE_443_NAME=mywebapp \
	--env SERVICE_443_TAGS=https  \
	-P ....
```
These tags can be used in the DNS lookup in Consul:
```
http.mywebapp.service.consul
https.mywebapp.service.consul
```
