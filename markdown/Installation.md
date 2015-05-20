## Hands-on: Installation

http://cargonauts.io/ha-docker-coreos-and-consul

!SUB
### Prerequisites
+ [Git] [+ GitBash for Windows]
+ [VirtualBox] 4.3.10 or greater.
+ [Vagrant] 1.6 or greater.

!SUB
### Hands-on: Start the cluster
```
git clone \
  https://github.com/mvanholsteijn/coreos-container-platform-as-a-service.git
cd coreos-container-platform-as-a-service
cd vagrant
vagrant up

```

!SUB
### Hands-on: Setup ssh and list machines with fleetctl

```
eval "$(ssh-agent)" 
ssh-add ~/.vagrant.d/insecure_private_key
vagrant ssh core-01 -- -A 

fleetctl list-machines
```
* if you want to use fleetctl from your local machine
```
export FLEETCTL_TUNNEL=127.0.0.1:2222
```

!SUB
### Hands-on: Verify the installation

* use systemctl on each machine 
* to check that the following services are running

```
loaded active running   consul-http-router-lb
loaded active running   consul-http-router
loaded active running   Consul Server Announcer
loaded active running   Registrator
loaded active running   Consul Server Agent
```

!NOTE
for node in 1 2 3 ; do
	vagrant ssh -c "systemctl | grep consul" core-0$node
done 

!SLIDE
## Hands-on: checkout the Consul Console

!SUB
![consul-console](images/consul-console.png)

* the consul console is listing on port 8500 on each machine
* setup a tunnel and navigate to http://localhost:8500
* how many instances of the http-router do you see running?

!SUB
### Hands-on: typing instruction
```
vagrant ssh core-01 -- -A -L8500:172.17.8.101:8500
open http://127.0.0.1:8500
```

!SLIDE
## Hands-on: Checkout Consul Templates

!SUB
## Hands-on: Checkout the HAProxy configuration

* use docker exec to look at the HAProxy template /haproxy.ctmpl of the consul-http-router-lb
* use docker exec to look at the HAProxy configuration  /etc/haproxy/haproxy.cfg of the consul-http-router-lb
* how many entries do you see listed?

!NOTE
vagrant ssh core-01 -- docker exec consul-http-router-lb cat /haproxy.ctmpl
vagrant ssh core-01 -- docker exec consul-http-router-lb cat /etc/haproxy/haproxy.cfg

!SUB
## Hands-on: Checkout the NGiNX configuration

* use docker exec to look at the NGiNX configuration  /nginx.ctmpl of the consul-http-router
* use docker exec to look at the NGiNX configuration  /etc/nginx/nginx.conf of the consul-http-router
* how many backends are registered?

!NOTE
vagrant ssh core-01 -- docker exec consul-http-router cat /nginx.ctmpl
vagrant ssh core-01 -- docker exec consul-http-router cat /etc/nginx/nginx.conf
