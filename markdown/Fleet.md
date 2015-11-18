## Hands-on Fleet
![CoreOS fleet](images/fleet-overview.png) <!-- .element: class="noborder" -->


!SUB
### Fleet
* Distributed init system on top of etcd
* Distribute services across a cluster
* Maintains required number of instances of a service
* Discover machines running in the cluster


!SUB
### Supported systemd unit types
* service  - defines a process
* socket - defines a socket for a service
* device - defines a device
* mount  - defines a filesystem mount
* automount - defines a auto filesystem mount
* timer - schedules the activation of another unit
* path - schedules the activation of a unit depending on file system changes

!SUB
### Systemd Unit Service File
Systemd unit files consists of three distinct sections.

* Unit  - describes this unit and it's dependencies
* Service - configures this unit
* X-Fleet - contains Fleet specific configurations for placement of the services
```
[Unit]
...
[Service]
...
[X-Fleet]
...
```

!SUB
### Service description and dependencies

In the unit section you describe the service and it's dependencies.
```
[Unit]
Description=      short of the service
Requires=         another service
PartOf=           another service
Before=           another service
After=            another service
Conflicts=        another service
OnFailure=      services to activate, when this one fails
RequiresMountsFor=  list of directories
```
As you can see, you can obtain fine control over the service's dependencies.
At a minimum, you should specify Description.


!SUB
### Start/stop commands

Commands to use to start and stop your docker containers.
```
[Service]
ExecStartPre=     command to run in preparation
ExecStart=        command to start the service
ExecStop=         command to stop all processes of the service
ExecStartPost=	command to run after successful start
ExecStopPost= 	command to run after service is stopped
```
Commands that exit with a non-zero exit causes the service to enter a failed state. If it is not fatal, use =- eg.
```
ExecStartPre=- /usr/bin/docker pull myimage
```


!SUB
### Introducing paas-monitor
* [paas-monitor](https://github.com/mvanholsteijn/paas-monitor) is a Docker web application to explore the deployment behaviour of PaaS systems.
```
/status - returns information about the instance
/environment - returns the environment of the instance
/  - starts a browser application that continuously calls /status and displays the results
```


!SUB
### Hand-on: running paas-monitor stand alone
To see the paas-monitor in application
```
docker run -p :1337:1337 mvanholsteijn/paas-monitor
open http://localhost:1337
```


!SUB
### Hands-on creating paas-monitor.service
* create a fleet unit service file, for  [mvanholsteijn/paas-monitor:latest](https://github.com/mvanholsteijn/paas-monitor)
* specify minimal [Unit] and [Service] section.
* expose all ports


!NOTE

\# paas-monitor.service
```
[Unit]
Description=paas-monitor
[Service]
ExecStart=/usr/bin/docker run -P mvanholsteijn/paas-monitor:latest
```


!SUB
### fleetctl 1/2
fleetctl is the application for controlling fleet unit files.
```
fleetctl submit          - sends a unit file to the cluster
fleetctl load            - targets a unit for a specific machine
fleetctl start           - request systemd to start the unit
fleetctl stop            - request systemd to stop unit
fleetctl destroy         - stops and removes the unit file
fleetctl status          - shows the status of a unit
```
submits only sends the unit files into etcd.
load assigns a unit file to a specific machine.
start/stop will actually request systemd to start/stop the unit.
You cannot update units. You must destroy and recreate.

!SUB
### fleetctl 2/2
fleetctl is the application for controlling fleet unit files.
```
fleetctl cat             - shows the unit file
fleetctl journal         - show unit journals
fleetctl list-units      - lists all active units
fleetctl list-unit-files - lists all unit files
```
All submitted files can be viewed with list-unit-files. All loaded, started or stopped units can be seen using list-units. status will show the status of an instance. As all output of the services is captured by journald, you can view stdout and stderr with fleetctl journal.


!SUB
### Hands-on - start/stop a service unit

* start the paas-monitor using fleetctl
* view the service by http://paas-monitor.127.0.0.1.xip.io:8080
* stop the paas-monitor. Check the state.
* explore the different unit states with commands destroy, submit, load.
* what status of the unit in after a stop?


!NOTE

\# Typing instructions
```bash
\# start service
fleetctl start paas-monitor.service
\# view app
open http://paas-monitor.127.0.0.1.xip.io:8080
\# stop
fleetctl stop paas-monitor.service
\# view state
fleetctl status paas-monitor.service \# ==> state -> failed
\# view destroy, submit and load
fleetctl destroy paas-monitor.service
fleetctl submit paas-monitor.service
fleetctl load paas-monitor.service
```


!SUB
### Successful exit status
As you may have noticed, a process that exits with a non-zero status is put in the state failed.

You can set SuccessExitStatus to a space separated list of valid numeric exit code or termination signal names. For instance:
```
[Service]
SuccessExitStatus=12 13 SIGTERM
```

!SUB
### Hands-on - determine success exit status

* add SuccessExitStatus to the paas-monitor unit.
* If correct, the unit will reach 'dead' state on stop.
* What happens if you kill the paas-monitor with SIGKILL?


!NOTE

\# paas-monitor.service
```
[Unit]
Description=paas-monitor
[Service]
ExecStart=/usr/bin/docker run -P mvanholsteijn/paas-monitor:latest
SuccessExitStatus=0 2
```

!SUB
### Restart policy
As you may have noticed, services are not automatically restarted. You need to specify a restart policy.

```
[Service]
Restart=          [no, always, on-success, on-failure, on-abnormal or on-abort]
```
Our advise for docker containers: **always**.


!SUB
### Restart policy options
|Restart settings/Exit causes|no|always|on-success|on-failure|on-abnormal|on-abort|
|----------------------------|--|------|----------|----------|-----------|--------|
|Clean exit code or signal| |X|X| | |
|Unclean exit code| |X| |X| |
|Unclean signal| |X| |X|X|
|Timeout| |X| |X|X|


!SUB
### Restart timeouts
Normally a restart occurs over 100ms, which may swamp your machine if there are persistent errors. Advised to set RestartSecs to a larger value (5-10 seconds).

```
[Service]
RestartSec=       between restarts
TimeoutStartSec=  timeout on start
```
Default TimeoutStartSec is 90 seconds, which is sometimes to short for pulling large docker images or pulling a large number of concurrent images.

!SUB
### Hands-on - restart policy

* add a restart policy to your paas-monitor.
* Now kill the process. What happens?


!NOTE

\# paas-monitor.service
```
[Unit]
Description=paas-monitor

[Service]
Restart=always
RestartSec=5
ExecStart=/usr/bin/docker run -P mvanholsteijn/paas-monitor:latest
SuccessExitStatus=0 2
```

!SUB
### Hands-on - Splitting pull from run

* Modify your unit file to pull the image before starting the container.


!NOTE
\# paas-monitor.service
```
[Unit]
Description=paas-monitor

[Service]
Restart=always
RestartSec=5
ExecStartPre=/usr/bin/docker pull mvanholsteijn/paas-monitor:latest
ExecStart=/usr/bin/docker run -P mvanholsteijn/paas-monitor:latest
SuccessExitStatus=0 2
```


!SUB
### Fleetctl machine commands
Fleet allows you to list all the machines in the cluster and log in to any of them.
```
fleetctl list-machines    - list machines in cluster
fleetctl ssh <machine-id> - ssh into machine
fleetctl ssh <unit-name> - ssh into machine running <unit>
```

!SUB
### Hands-on - machines

* What are the machine ids of the machines in your cluster?
* Kill the paas-monitor by logging in to the machine using fleetctl.


!NOTE

\# Typing instructions
```bash
fleetctl list-machines
fleetctl ssh paas <<EOF
ps -ef | grep /paas-monitor\$ | grep -v grep | awk '{print $2}' | xargs sudo kill -9
EOF
```


!SUB
### Fleet specific options
The X-Fleet section allows you to influence the machines the units should
be started on.
```
[X-Fleet]
MachineID=        Select a specific machine
MachineOf=        Select a machine that hosts a specific unit
MachineMetadata=  Select a machine with matching metadata
Conflicts=        Select a machine not running another unit (pattern)
Global=           Select all machines in the cluster
```


!SUB
### Hands-on - specific machine

* Deploy paas-monitor on core-02 only.


!NOTE
\# Typing instructions
```bash
\# Get the machine id of core-02
$(fleetctl list-machines -no-legend -fields=machine,ip -full | grep 172.17.8.102 | awk '{print $1}')
```

\# paas-monitor.service
```
[Unit]
Description=paas-monitor
[Service]
Restart=always
RestartSec=5
ExecStartPre=/usr/bin/docker pull mvanholsteijn/paas-monitor:latest
ExecStart=/usr/bin/docker run -P mvanholsteijn/paas-monitor:latest
SuccessExitStatus=0 2
[X-Fleet]
MachineID=<machine-id-core-02>
```


!SUB
### Hands-on - global unit

* Deploy paas-monitor on all machines.
* view the result on http://paas-monitor.127.0.0.1.xip.io:8080
* If you use global=true what is the effect of fleetctl status and journal?


!NOTE
\# paas-monitor.service
```
[Unit]
Description=paas-monitor
[Service]
Restart=always
RestartSec=5
ExecStartPre=/usr/bin/docker pull mvanholsteijn/paas-monitor:latest
ExecStart=/usr/bin/docker run -P mvanholsteijn/paas-monitor:latest
SuccessExitStatus=0 2
[X-Fleet]
Global=true
```

!SUB
### CoreOS systemd essential commands
Fleet submits the units to a machine as normal systemd units.
```
systemctl          - lists all system units
systemctl status   - of a specific unit
systemctl cat      - shows the content of a unit file
journalctl         - show all log output
journalctl -u      - show log output of a unit
docker             - manage docker on local host
```


!SUB
### hands-on: systemd commands
* login to a machine running  paas-monitor.
* Inspect the status of paas-monitor.
* Inspect the systemd unit file
* use journalctl to inspect the logs.
* How are the output of systemctl and journalctl different from fleetctl?


!NOTE
\# Typing instructions
```bash
\# Login to the first machine
fleetctl ssh $(fleetctl list-machines -no-legend -fields=machine -full | head -1)

\# On CoreOS machine
systemctl status paas-monitor
systemctl cat paas-monitor
journalctl -u paas-monitor
```


!SUB
### Template files
* If you want to start multiple instances of a unit, you can use template files.
* template unit names end in a @.
    * eg. paas-monitor@.service
* You cannot start a template file, without reaping havoc on fleet.
* after submitting a template file, you can start load/start instances.

```bash
fleetctl submit paas-monitor@.service
fleetctl start paas-monitor@1.service
fleetctl start paas-monitor@{2..4}.service
```


!SUB
### Hands-on: paas-monitor template

* create a template file for paas-monitor.
* start 4 instances of paas-monitor.


!NOTE
\# paas-monitor@.service

```
[Unit]
Description=paas-monitor
[Service]
Restart=always
RestartSec=5
ExecStartPre=/usr/bin/docker pull mvanholsteijn/paas-monitor:latest
ExecStart=/usr/bin/docker run -P mvanholsteijn/paas-monitor:latest
SuccessExitStatus=0 2
```

\# Typing instructions

```bash
$ fleetctl submit paas-monitor@.service
$ fleetctl load paas-monitor@{1..4}
$ fleetctl start paas-monitor@{1..4}
```

!SUB
### Unit file specifiers
In unit files you can reference meta information about the unit through specifiers. Below are the most important specifiers.

|Specifier|Details|
|---------|-------|
| %n | full unit name |
| %p | prefix name for instantiated units |
| %i | instance name for instantiated units |
| %H | host name running the unit |
| %% | percent character |


!SUB
### Hands-on: matching Docker container names with unit names

* Modify your unit file to match the docker instance container name with the fleet unit name.

!NOTE
\# paas-monitor@.service
```
[Unit]
Description=paas-monitor
[Service]
Restart=always
RestartSec=5
ExecStartPre=-/usr/bin/docker rm -f %p-%i
ExecStartPre=/usr/bin/docker pull mvanholsteijn/paas-monitor:latest
ExecStart=/usr/bin/docker run --name %p-%i -P mvanholsteijn/paas-monitor:latest
SuccessExitStatus=0 2
```


!SUB
### Fleet Docker unit files
The following unit files is a good template for Docker applications.
```
[Unit]
Description=<image>

[Service]
Restart=always
RestartSec=30
TimeoutStartSec=3m

ExecStartPre=-/usr/bin/docker rm -f %p-%i
ExecStartPre=/usr/bin/docker pull <image>

ExecStart=/usr/bin/docker run --name %p-%i -P <image>

ExecStop=/usr/bin/docker stop %p-%i
SuccessExitStatus=0 SIGTERM

```


!SUB
### Environment settings

Environment variables to be loaded before running any of the commands.  Normally set to /etc/environment to obtain:

- COREOS_PRIVATE_IPV4 - private IP address of this machine
- COREOS_PUBLIC_IPV4 - public IP address of this machine

Of course, you can add other environment files

```
[Service]
EnvironmentFile=  to load before running commands
```
You may reference these environment variables in your commands, for instance:
```
[Service]
EnvironmentFile=/etc/environment
ExecStart=/bin/echo ${COREOS_PRIVATE_IPV4} and ${COREOS_PUBLIC_IPV4}
```
Shell substitution is not supported. If required, you need to start a shell yourself.
```
[Service]
ExecStart=/bin/sh -c 'exec /bin/echo $(date)'
```


!SUB
### Killing remaining processes
After the stop command is run, any processes still running will be sent a SIGTERM and optionally a SIGHUP.
If after TimeoutStopSec seconds a process is still running, it will be sent a SIGKILL.

```
[Service]
KillSignal=	  signal to use to request running processes to stop, default SIGTERM
TimeoutStopSec=  seconds to wait before sending SIGKILL, default 10s.

SendSIGKILL=	 **yes**/no
SendSIGHUP=	  yes/**no**
```

!SUB
### Kill Mode

KillMode is used to determine the kill behavior for the service processes. The default is control-group, which is fine for Docker containers.
```
[Service]
KillMode=	    one of control-group, process, mixed or none
```

!SUB
### Kill Mode options

|KillMode|description|
|--------|-----------|
|control-group|kills all process in control group running after stop command completed|
|process|kills only the main process|
|mixed|sends SIGTERM to main process, SIGKILL to other process in group|
|none|no processes are killed|
