## Hands-on
# Deploy Application

!SUB
## Deploy Application
Deploy 6 instances of the paas-monitor using fleetctl and this unit file [paas-monitor@.service](https://raw.githubusercontent.com/mvanholsteijn/coreos-container-platform-as-a-service/master/fleet-units/paas-monitor/paas-monitor%40.service)
```
[Unit]
Description=%p

[Service]
Restart=always
RestartSec=15
TimeoutStartSec=2m

ExecStartPre=-/usr/bin/docker kill %p-%i
ExecStartPre=-/usr/bin/docker rm %p-%i

ExecStart=/bin/sh -c "/usr/bin/docker run --rm --name %p-%i \
	--env RELEASE=v1 \
	--env SERVICE_NAME=%p \
	--env SERVICE_TAGS=http \
	-P \
	--dns $(ifconfig docker0 | grep 'inet ' | awk '{print $2}') \
	--dns-search=service.consul \
	mvanholsteijn/paas-monitor"

ExecStop=/usr/bin/docker stop %p-%i

SuccessExitStatus=2
SyslogIdentifier=%p-%i
```


!SUB
## Check deployment results
Open [paas-monitor.127.0.0.1.xip.io:8080](http://paas-monitor.127.0.0.1.xip.io:8080)
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
\# Typing instructions
```bash
\# Submit the paas-monitor template
fleetctl submit paas-monitor@.service
\# Load the paas-monitor app
fleetctl load paas-monitor@{1..6}.service
\# Start the paas-monitor app
fleetctl start paas-monitor@{1..6}.service
\# Check the status of the paas-monitor app
fleetctl list-units | grep paas-monitor
```


!SLIDE
## Hands-on
# Rolling upgrade

!SUB
## Rolling upgrade
* Change the environment variable `RELEASE` to v2 in the unit file
* Perform a rolling upgrade using fleetctl
* Continuously watch the monitor!

!NOTE
\# Typing instructions
```bash
\# Mimic a new release of the template
sed -i -e 's/--env RELEASE=[^ ]*/--env RELEASE=v2/'  paas-monitor\@.service
\# Destroy the old and submit new unit template file
fleetctl destroy paas-monitor@.service
fleetctl submit paas-monitor@.service
\# Load 6 new instances of the paas-monitor
fleetctl load paas-monitor@{1..6}-v2.service
\# Start 6 new instances of the paas-monitor
fleetctl start paas-monitor@{1..6}-v2.service
\# List stop and destroy the original 6 instances
fleetctl list-units | grep 'paas-monitor\@[1-6]-v2.service' | grep running
fleetctl stop paas-monitor@{1..6}.service
fleetctl destroy paas-monitor@{1..6}.service
```

!SLIDE
## Hands-on
# Killing an instance

!SUB
## Killing an instance
* Start a docker event stream.
* Kill one of the paas-monitor instances
* Watch the paas-monitor. What happens?

!NOTE
\# Typing instructions
```bash
\# in separate window
open http://paas-monitor.127.0.0.1.xip.io:8080
vagrant ssh core-01 --  docker events'
vagrant ssh core-01 --  'docker kill $(docker ps | grep paas-monitor | awk "{ print \$NF;}" | head -1 ) '
```

!SLIDE
## Hands-on
# Killing a machine

!SUB
## Killing a machine
* Stop the machine `core-03`
* Watch the paas-monitor. What happens?

!NOTE
\# Typing instructions
```bash
open http://paas-monitor.127.0.0.1.xip.io:8080
vagrant ssh core-03 --  sudo shutdown -h now'
```
