# Hands-on: Installation

#### Prerequisites:
	* [Git] [+ GitBash for Windows]
	* [VirtualBox] 4.3.10 or greater.
	* [Vagrant] 1.6 or greater.

#### Run the vagrant CoreOS cluster:
	* Clone the Github repository:
	  https://github.com/mvanholsteijn/coreos-container-platform-as-a-service.git
	* Navigate to the vagrant folder of your cloned repository
	* Execute: vagrant up

#### Setup ssh and list machines with fleetctl:
	* eval "$(ssh-agent)" [Windows only]
	* ssh-add ~/.vagrant.d/insecure_private_key
	* vagrant ssh core-01 -- -A
	* fleetctl list-machines




# Verify the installation

#### Run the following code:
	for node in 1 2 3 ; do
		vagrant ssh -c "systemctl | grep consul" core-0$node
	done 

#### For each node you should see the following services running
	* loaded active running   consul-http-router-lb
	* loaded active running   consul-http-router
	* loaded active running   Consul Server Announcer
	* loaded active running   Registrator
	* loaded active running   Consul Server Agent