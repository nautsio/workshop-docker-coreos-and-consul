## Hands-on: Deploy Applications

#### Submit the paas-monitor template
	fleetctl submit paas-monitor@.service

#### Load the paas-monitor app
	fleetctl load paas-monitor@{1..6}.service

#### Start the paas-monitor app
	fleetctl start paas-monitor@{1..6}.service

#### Check the status of the paas-monitor app
	fleetctl list-units | grep paas-monitor

####### windows users: 
	use scp to copy the file to core-01 and run fleetctl from core-01
	example: scp ../fleet-units/paas-monitor/paas-monitor@.service core@172.17.8.101:~




## Hands-on: Check the application

#### Open the following URL from your browser:
	 http://paas-monitor.127.0.0.1.xip.io:8080

#### Press the start button and check the displayed table
	host				rel	message				#C	ART	LRT
	47ea72be3817:1337	v1	Hello World from v1	53	8	11
	cc4227a493d7:1337	v1	Hello World from v1	59	11	8
	04a58012910c:1337	v1	Hello World from v1	54	7	6
	090caf269f6a:1337	v1	Hello World from v1	58	7	7
	096d01a63714:1337	v1	Hello World from v1	53	7	9
	d43c0744622b:1337	v1	Hello World from v1	55	7	6




## Hands-on: Rolling upgrade [1]

#### Mimic a new release by changing the RELEASE env variable in the paas-monitor application unit template.
	sed -i -e 's/--env RELEASE=[^ ]*/--env RELEASE=v2/' paas-monitor\@.service

#### Destroy the paas-monitor unit service
	fleetctl destroy paas-monitor@.service

#### Submit the updated unit template
	fleetctl submit paas-monitor@.service




## Hands-on: Rolling upgrade [2]

#### Load 6 new instances of the paas-monitor
	fleetctl load paas-monitor@1{1..6}.service

#### Start 6 new instances of the paas-monitor 
	fleetctl start paas-monitor@1{1..6}.service

#### List stop and destroy the original 6 instances
	fleetctl list-units | grep 'paas-monitor\@1[1-6].service' | grep running
	fleetctl stop paas-monitor@{1..6}.service
	fleetctl destroy paas-monitor@{1..6}.service