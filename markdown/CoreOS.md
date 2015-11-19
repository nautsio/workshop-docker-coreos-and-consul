![CoreOS logo](images/coreos.svg)
# CoreOS

!SUB
<!-- .element: class="center" -->
## CoreOS Features
Linux for Massive Server Deployments
![CoreOS features](images/coreos-features.png)

!SUB
## CoreOS Overview
![CoreOS features](images/coreos-overview.png)

!SUB
<!-- .element: class="center" -->
## The only way to run stuff on CoreOS: Docker
![Docker logo](images/docker.svg)


!SLIDE
![CoreOS fleet](images/fleet-overview.png)
# Fleet

!SUB
## Fleet features
* Basically a distributed SystemD/init system
* Distribute services across a cluster
* Maintains required number of instances of a service
* Discover machines running in the cluster
* Easy way to SSH into the machine running a job

[_coreos.com/fleet_](https://coreos.com/fleet)

!SUB 
## Fleet uses
* SystemD like unit files for service definitions
* [etcd](https://coreos.com/etcd) distributed key/value store
* Consists of the an engine and agents

[_coreos.com/fleet_](https://coreos.com/fleet)
