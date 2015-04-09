### CoreOS 
![CoreOS logo](images/logo-coreos.png) 


!SUB
### CoreOS Features
Linux for Massive Server Deployments
![CoreOS features](images/coreos-features.png) <!-- .element: class="noborder" -->

!SUB
### CoreOS Overview
![CoreOS features](images/coreos-overview.png) <!-- .element: class="noborder" -->

!SLIDE
### Docker 
![Docker](images/docker.jpg) <!-- .element: style="width: 50%;" class="noborder" -->

The only way to install and run stuff on CoreOS!


!SLIDE
### Etcd
![CoreOS etcd](images/etcd.png) <!-- .element: style="width: 50%;" class="noborder" -->

* Distributed Key/Value store
* hierarchical directory
* REST API

!SUB
### etcdctl essential commands
```
etcdctl mk           make a new key with a given value
etcdctl rm           remove a key
etcdctl set          set the value of a key
etcdctl get          get the value of a key
etcdctl update       update an existing key with a given value

etcdctl rmdir        removes an empty directory or key
etcdctl mkdir        make a new directory
etcdctl ls           list the directory

etcdctl watch        watch a key for changes
etcdctl exec-watch   watch a key for changes and exec an executable
```

!SLIDE
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

!SUB
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
### Systemd Unit Service File
```
[Unit]
Description=      of the service
PartOf=           another service
After=            another service 

[Service]
Restart=          indicates whether a restart is needed
EnvironmentFile=  to load before running commands

ExecStartPre=     command to run in preparation
ExecStart=        command to start the service
ExecStop=         command to stop the service

RestartSec=       between restarts
TimeoutStartSec=  timeout on start

SuccessExitStatus= defines succesfull exits.
SyslogIdentifier=  identifier of the log entries.
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
### Template files
* unit names ending in @ are a template
    * eg. paas-monitor@.service
* the part after @ is accessible in the unit as %i

```
ExecStart=/usr/bin/docker run --name <instance-name>-%i
ExecStartPre=-/usr/bin/docker kill <instance-name>-%i
ExecStartPre=-/usr/bin/docker rm <instance-name>-%i
ExecStartPre=/usr/bin/docker pull <image>
```

* Now you can create,start,stop instances
    * paas-monitor@1.service
    * paas-monitor@mine.service
    * paas-monitor@yours.service

!SUB
### Fleetctl essential unit commands
```
fleetctl submit          - sends a unit file to the cluster
fleetctl load            - targets a unit for a specific machine
fleetctl start           - request systemd to start the unit
fleetctl stop            - request systemd to stop unit
fleetctl destroy         - stops and removes the unit file
fleetctl status          - shows the status of a unit
fleetctl cat             - shows the unit file
fleetctl journal         - show unit journals
fleetctl list-units      - lists all active units
fleetctl list-unit-files - lists all unit files
```

!SUB
### Fleetctl other commands
```
fleetctl list-machines    - list machines in cluster 
fleetctl ssh <machine-id> - ssh into machine
```

!SUB
### CoreOS essential commands
```
systemctl          - lists all system units
systemctl status   - of a specific unit
systemctl cat      - shows the content of a unit file
journalctl         - show all log output
journalctl -u      - show log output of a unit
docker             - manage docker on local host
```
