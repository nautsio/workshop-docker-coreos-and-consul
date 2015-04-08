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

#### Setup ssh and verify the installation:
	* eval "$(ssh-agent)" [Windows only]
	* ssh-add ~/.vagrant.d/insecure_private_key
	* vagrant ssh core-01 -- -A
	* fleetctl list-machines
