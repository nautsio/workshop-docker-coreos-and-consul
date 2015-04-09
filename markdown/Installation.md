## Hands-on: Installation

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
vagrant up

```

!SUB
### Hands-on: Setup ssh and list machines with fleetctl

```
eval "$(ssh-agent)" 
ssh-add ~/.vagrant.d/insecure_private_key
vagrant ssh core-01 -- -A 

export FLEETCTL_TUNNEL=127.0.0.1:2222
fleetctl list-machines
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

!SUB
### Hands-on: checkout the Consul Console
![consul-console](images/consul-console.png)

* the consul console is listing on port 8500 on each machine
* setup a tunnel and navigate to http://localhost:8500


!NOTE
vagrant ssh core-01 -- -A -L8500:172.17.8.101:8500
