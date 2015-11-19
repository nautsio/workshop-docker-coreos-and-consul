# Setup

!SUB
## Prerequisites
+ [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
+ [VirtualBox](https://www.virtualbox.org/wiki/Downloads) (4.3.10+)
+ [Vagrant](https://docs.vagrantup.com/v2/installation/index.html) (1.6+)
+ `fleetctl` installed

!SUB
## Start the cluster
```
git clone https://github.com/mvanholsteijn/coreos-container-platform-as-a-service.git
cd coreos-container-platform-as-a-service/vagrant
vagrant up
. ./setenv
```

!SUB
## Setup ssh and list machines with fleetctl

```
brew install fleetctl
eval "$(ssh-agent)"
ssh-add ~/.vagrant.d/insecure_private_key
vagrant ssh core-01 -- -A

fleetctl list-machines
```

!SUB
## Verify the installation

* use fleetctl to check that all services are running
```
ssh vagrant core-01 -- -t watch fleetctl list-units
```
* wait until it looks like this:
````
UNIT                                    MACHINE                         ACTIVE  SUB
consul-http-router.service              6e1a9adb.../172.17.8.102        active  running
consul-http-router.service              9b969332.../172.17.8.101        active  running
consul-http-router.service              dd9eb383.../172.17.8.103        active  running
consul-server-registrator.service       6e1a9adb.../172.17.8.102        active  running
consul-server-registrator.service       9b969332.../172.17.8.101        active  running
consul-server-registrator.service       dd9eb383.../172.17.8.103        active  running
consul-server.service                   6e1a9adb.../172.17.8.102        active  running
consul-server.service                   9b969332.../172.17.8.101        active  running
consul-server.service                   dd9eb383.../172.17.8.103        active  running
```


!SLIDE
# checkout Consul UI

!SUB
## Consul UI
![consul-console](images/consul-console.png)

* the Consul UI is listening on port 8500 on each machine
* setup a tunnel and navigate to http://localhost:8500
* how many instances of the consul-dns service do you see running?

!NOTE
\# Typing instruction
```bash
vagrant ssh core-01 -- -A -L8500:172.17.8.101:8500
open http://127.0.0.1:8500
```

!SLIDE
# Hands-on: Checkout Consul Template

!SUB
## Hands-on: Checkout the NGiNX configuration

* use docker exec to look at the NGiNX configuration `/nginx.ctmpl` of the consul-http-router
* use docker exec to look at the NGiNX configuration `/etc/nginx/nginx.conf` of the consul-http-router
* how many backends are registered?

!NOTE
\# Typing instructions
```bash
vagrant ssh core-01 -- docker exec consul-http-router cat /nginx.ctmpl
vagrant ssh core-01 -- docker exec consul-http-router cat /etc/nginx/nginx.conf
```
