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

!SLIDE
### Fleet
* Fleet (re)schedules units across the cluster
* uses systemd unit configuration files
* units managed by systemd

!SUB
### Unit types
* service 
* socket 
* device 
* mount point 
* swap file or partition
* start-up target 
* watched file system path
* timer 

!SUB
## Systemd Unit Service File
```
[Unit]
Description=      of the service
PartOf=           another service
After=            another service 

[Service]
Restart=          indicates whether a restart is needed
RestartSec=       between restarts
EnvironmentFile=  to load before running commands
TimeoutStartSec=  timeout on start
ExecStartPre=     command to run in preparation
ExecStart=        command to start the service
ExecStop=         command to stop the service
```

!SUB
### Fleet specific options
```
...
[X-Fleet]

MachineID=        Select a specific machine
MachineOf=        Select a machine that hosts a specific unit 
MachineMetadata=  Select a machine with matching metadata 
Conflicts=        Select a machine not rnning another unit (pattern)
Global=           Select all machines in the cluster
```

!SUB
### Fleet Docker template

```
[Service]
EnvironmentFile=/etc/environment

ExecStart=/bin/sh -c "/usr/bin/docker run --name <instance-name>"

Restart=always
RestartSec=30
TimeoutStartSec=3m

ExecStartPre=-/usr/bin/docker kill <instance-name>
ExecStartPre=-/usr/bin/docker rm <instance-name>
ExecStartPre=/usr/bin/docker pull <image>

ExecStop=/usr/bin/docker stop <instance-name>
```

!SUB
### Fleetctl essential unit commands
```
fleetctl submit    - sends a unit file to the cluster
fleetctl load      - targets a unit for a specific machine
fleetctl start     - request systemd to start the unit
fleetctl stop      - request systemd to stop unit
fleetctl destroy   - stops and removes the unit file
```
