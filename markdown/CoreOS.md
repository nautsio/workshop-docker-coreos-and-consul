### CoreOS 
![CoreOS logo](images/logo-coreos.png) 


!SUB
### CoreOS Features
Linux for Massive Server Deployments
![CoreOS features](images/coreos-features.png) <!-- .element: class="noborder" -->

!SUB
### CoreOS Overview
![CoreOS features](images/coreos-overview.png) <!-- .element: class="noborder" -->

!SUB
### Etcd
![CoreOS etcd](images/etcd.png) <!-- .element: style="width: 50%;" class="noborder" -->

* Distributed Key/Value store
* hierarchical directory
* REST API

!SUB
### Fleet
![CoreOS fleet](images/fleet-overview.png) <!-- .element: class="noborder" -->

* Distributed init system / Scheduler / Nanny
* uses etcd, works on top of Systemd

!NOTE
-Distributed init system
-Deploy docker containers on arbitrary hosts in a cluster
-Distribute services across a cluster 
-Maintain N instances of a service, re-scheduling on machine failure
-Discover machines running in the cluster
