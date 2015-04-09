### Hands-on: Deploy Applications

```shell
#### Submit the paas-monitor template
fleetctl submit paas-monitor@.service

#### Load the paas-monitor app
fleetctl load paas-monitor@{1..6}.service

#### Start the paas-monitor app
fleetctl start paas-monitor@{1..6}.service

#### Check the status of the paas-monitor app
fleetctl list-units | grep paas-monitor
```


!SUB
### Hands-on: Check the application
Open http://paas-monitor.127.0.0.1.xip.io:8080 and click 'start'

```
       host               rel      message                   #C       ART     LRT
       47ea72be3817:1337  v1       Hello World from v1       53       8       11
       cc4227a493d7:1337  v1       Hello World from v1       59       11      8
       04a58012910c:1337  v1       Hello World from v1       54       7       6
       090caf269f6a:1337  v1       Hello World from v1       58       7       7
       096d01a63714:1337  v1       Hello World from v1       53       7       9
       d43c0744622b:1337  v1       Hello World from v1       55       7       6
```


!SUB
### Hands-on: Rolling upgrade

```sh
#### Mimic a new release of the template 
sed -i -e 's/--env RELEASE=[^ ]*/--env RELEASE=v2/'  paas-monitor\@.service

#### Destroy the old and submit new unit template file 
fleetctl destroy paas-monitor@.service
fleetctl submit paas-monitor@.service

#### Load 6 new instances of the paas-monitor
fleetctl load paas-monitor@{1..6}-v2.service

#### Start 6 new instances of the paas-monitor 
fleetctl start paas-monitor@{1..6}-v2.service

#### List stop and destroy the original 6 instances
fleetctl list-units | grep 'paas-monitor\@[1-6]-v2.service' | grep running
fleetctl stop paas-monitor@{1..6}.service
fleetctl destroy paas-monitor@{1..6}.service
```
