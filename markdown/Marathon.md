### Hands-on: Using Mesos and Marathon (Advanced)

* create a deployment for 1 zookeeper, 1 mesos master and 1 marathon instance
* and a zookeeper slave on each node
* and deploy the paas-monitor using marathon using [paas-monitor-marathon.json](https://raw.githubusercontent.com/mvanholsteijn/paas-monitor/master/marathon.json)

!SUB
### Hands-on: Create a zookeeper unit
* Create a zookeeper unit file using cargonauts/zookeeper:0.0.1
* use the host network
* expose and publish on port 2181, tag it as a 'zk'  service

!SUB
### Hands-on: Tip Zookeeper unit file
* The resulting Docker run command roughly should look like this:

```
/usr/bin/docker run 
    --name %p 
    --net host
    --publish :2181:2181 
    --env SERVICE_NAME=zookeeper
    --env SERVICE_2181_TAGS=zk
    cargonauts/zookeeper:0.0.1
```

!SUB
### Create the Mesos Master unit
* Create a zookeeper unit file using mesosphere/mesos-master:0.23.0-1.0.ubuntu1404
* expose and publish on port 5050 and tag it as 'http' service
* Start after the zookeeper
* lookup the zookeeper IP address in Consul using curl or dig on startup.
* wait for the zookeeper service to become available
* use host networking
* more info on command line options http://mesos.apache.org/documentation/latest/configuration/


!SUB
### Hands-on: Tip Mesos Master unit file
* The resulting Docker run command roughly should look like this:

```
docker run 
      --name x-mesos-master
      --net host
      --publish :5050:5050
      --env SERVICE_NAME=mesos
      --env SERVICE_TAGS=http
      --env MESOS_ZK=zk://<zookeeper-ip>:<zookeeper-port-from-consul>/mesos
      --env MESOS_ADVERTISE_IP=$COREOS_PRIVATE_IPV4
      --env MESOS_PORT=5050
      --env MESOS_QUORUM=1
      --env MESOS_CLUSTER=single-node-mesos
      mesosphere/mesos-master:0.23.0-1.0.ubuntu1404
```

!SUB
### Create the Mesos Slave unit
* Create a zookeeper unit file using mesosphere/mesos-slave:0.23.0-1.0.ubuntu1404
* expose and publish on port 5051 
* Start after the zookeeper and after the mesos-master
* lookup the zookeeper IP address in Consul using curl or dig on startup.
* wait for the zookeeper service to become available
* use host networking
* more info on command line options http://mesos.apache.org/documentation/latest/configuration/

!SUB
### Hands-on: Tip Mesos Slave unit file
* The resulting Docker run command roughly should look like this:

```
/usr/bin/docker run 
        --name x-%p 
        --privileged 
        --net host 
        --publish :5051:5051 
        --volume /var/lib/docker/btrfs/subvolumes:/var/lib/docker/btrfs/subvolumes 
        --volume /var/run/docker.sock:/var/run/docker.sock 
        --volume /sys:/sys 
        --volume /cgroup:/cgroup 
        --volume /usr/bin/docker:/usr/bin/docker 
        --volume /lib/libdevmapper.so.1.02:/lib/x86_64-linux-gnu/libdevmapper.so.1.02 
        --volume /dev/log:/dev/log 
        --env SERVICE_NAME=mesos-slave 
        --env MESOS_MASTER=zk://<zookeeper-address>:<zookeeper-port>/mesos 
        --env MESOS_EXECUTOR_REGISTRATION_TIMEOUT=5mins 
        --env MESOS_ISOLATOR=cgroups/cpu,cgroups/mem 
        --env MESOS_CONTAINERIZERS=docker,mesos 
        mesosphere/mesos-slave:0.23.0-1.0.ubuntu1404
```

!SUB
### Create the Marathon unit
* Create a marathon unit file using mesosphere/marathon:v0.10.0
* expose and publish on port 8888 tag it as 'http'
* use host networking
* Start after the zookeeper, the mesos-master and mesos-slave
* lookup the zookeeper IP address in Consul using curl or dig on startup.
* wait for the zookeeper service to become available
* Read more https://hub.docker.com/r/mesosphere/marathon

!SUB
### Hands-on: Tip Mesos Slave unit file
* The resulting Docker run command roughly should look like this:

```
   /usr/bin/docker run 
        --name x-%p 
        --net host 
        --publish :8888:8888 
        --env SERVICE_NAME=marathon 
        --env SERVICE_TAGS=http 
        mesosphere/marathon:v0.10.0  
        --master $ZOOKEEPERS/mesos 
        --zk $ZOOKEEPERS/marathon 
        --http_port 8888
```

!SUB
### Hands-on: Start the paas-monitor application
* Now create a [marathon application definition](https://mesosphere.github.io/marathon/docs/native-docker.html) for the paas-monitor.
* Post it to your [Marathon instance](http://marathon.127.0.0.1.xip.io:8080/v2/apps)
* Open it http://paas-monitor.127.0.0.1.xip.io:8080
* updating the application definition, watch the rolling upgrade

!SUB
### Hands-on: Typing instruction
* typing Instruction:
```
# Create and start
curl -s https://raw.githubusercontent.com/mvanholsteijn/paas-monitor/master/marathon.json | \
	curl -d @- -X POST -H 'Content-Type: application/json' http://marathon.127.0.0.1.xip.io:8080/v2/apps

# Update application
curl -s https://raw.githubusercontent.com/mvanholsteijn/paas-monitor/master/marathon.json | \
	sed -e  's/"RELEASE":.*/"RELEASE": "mesos-1.0",/'  |\
	curl -d @- -X PUT -H 'Content-Type: application/json' http://marathon.127.0.0.1.xip.io:8080/v2/apps/paas-monitor
```
