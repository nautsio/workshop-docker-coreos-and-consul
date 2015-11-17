## Hands-on: Rolling Upgrade Application

!SUB
### Hands-on: Deploy Application
* Deploy 6 instances of the paas-monitor using fleetctl and this unit file --> [paas-monitor@.service](https://raw.githubusercontent.com/mvanholsteijn/coreos-container-platform-as-a-service/master/fleet-units/paas-monitor/paas-monitor%40.service)  
* Open http://paas-monitor.127.0.0.1.xip.io:8080 

```
       host               rel      message                   #C       ART     LRT
       47ea72be3817:1337  v1       Hello World from v1       53       8       11
       cc4227a493d7:1337  v1       Hello World from v1       59       11      8
       04a58012910c:1337  v1       Hello World from v1       54       7       6
       090caf269f6a:1337  v1       Hello World from v1       58       7       7
       096d01a63714:1337  v1       Hello World from v1       53       7       9
       d43c0744622b:1337  v1       Hello World from v1       55       7       6
```


!NOTE
### Hands-on: typing instructions

#### Submit the paas-monitor template
fleetctl submit paas-monitor@.service

#### Load the paas-monitor app
fleetctl load paas-monitor@{1..6}.service

#### Start the paas-monitor app
fleetctl start paas-monitor@{1..6}.service

#### Check the status of the paas-monitor app
fleetctl list-units | grep paas-monitor


!SLIDE
## Hands-on: Rolling upgrade

!SUB
### Hands-on: Rolling upgrade
* Change the environment variable RELEASE to v2 in the unit file
* perform a rolling upgrade using fleetctl
* continuously watch the monitor!


!NOTE
### Hands-on: typing instructions
```shell
# Mimic a new release of the template 
sed -i -e 's/--env RELEASE=[^ ]*/--env RELEASE=v2/'  paas-monitor\@.service

# Destroy the old and submit new unit template file 
fleetctl destroy paas-monitor@.service
fleetctl submit paas-monitor@.service

# Load 6 new instances of the paas-monitor
fleetctl load paas-monitor@{1..6}-v2.service

# Start 6 new instances of the paas-monitor 
fleetctl start paas-monitor@{1..6}-v2.service

# List stop and destroy the original 6 instances
fleetctl list-units | grep 'paas-monitor\@[1-6]-v2.service' | grep running
fleetctl stop paas-monitor@{1..6}.service
fleetctl destroy paas-monitor@{1..6}.service
```

!SLIDE
## Hands-on: killing an instance

!SUB
### Hands-on: killing an instance
* start a docker event stream.
* kill one of the paas-monitor instance.
* watch the paas-monitor. What happens?

!NOTE
### Hands-on: typing instructions
# in seperate window
open http://paas-monitor.127.0.0.1.xip.io:8080
vagrant ssh core-01 --  docker events'
vagrant ssh core-01 --  'docker kill $(docker ps | grep paas-monitor | awk "{ print \$NF;}" | head -1 ) '

!SLIDE
## Hands-on: killing a machine

!SUB
### Hands-on: killing a machine
* stop the machine core-03
* watch the paas-monitor. What happens?

!NOTE
### Hands-on: typing instructions
open http://paas-monitor.127.0.0.1.xip.io:8080
vagrant ssh core-03 --  sudo shutdown -h now'
