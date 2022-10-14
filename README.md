# 5G-CLARITY-AI-ENGINE - Based on OpenFaaS

The AI Engine is largely based on OpenFaaS, but provides additional ML monitoring functionality. And a template to deploy AI Function is also provided.

## Requirement for a VM is 4 VCPUs, 8 GB RAN and 40 GB HDD.

## Deployment on Ubuntu 18 or up.
## Steps to Deploy Docker, 
if already installed please go to the "Steps to Deploy Docker Swarm" 
1. Update OS
``` Bash
sudo apt-get update -y && sudo apt-get upgrade -y
```
2. Install dependency packages with the below command:
``` Bash 
sudo apt install apt-transport-https ca-certificates curl software-properties-common lsb-release
 ```
3. Add docker key:
``` Bash 
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker-archive-keyring.gpg
```
4. Add docker repository to your hosts with the following commands:
``` Bash 
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
5. Update package
``` Bash 
sudo apt update
```
6. Ensure that you are going to install from the official Docker repo instead of default Ubuntu repo.
``` Bash 
$ apt-cache policy docker-ce
```
It must display something like the following description:
``` Bash 
docker-ce:
  Installed: (none)
  Candidate: 5:20.10.17~3-0~ubuntu-jammy
  Version table:
     5:20.10.17~3-0~ubuntu-jammy 500
        500 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages
     5:20.10.16~3-0~ubuntu-jammy 500
        500 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages
     5:20.10.15~3-0~ubuntu
     -jammy 500
        500 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages
     5:20.10.14~3-0~ubuntu-jammy 500
        500 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages
     5:20.10.13~3-0~ubuntu-jammy 500
        500 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages
```
7. Install Docker CE on Ubuntu2
``` Bash 
sudo apt install docker-ce
```
8. After installing, Docker daemon should be started. Confirm the status by running the below command:
``` Bash
$ systemctl status docker
```
After run the comman above you must see th following screen.
``` Bash 
● docker.service - Docker Application Container Engine 
    Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled) 
    Active: active (running) since Sat 2020-10-17 16:28:08 EAT; 57s ago 
TriggeredBy: ● docker.socket 
      Docs: https://docs.docker.com 
  Main PID: 2807 (dockerd) 
     Tasks: 8 
    Memory: 37.5M 
    CGroup: /system.slice/docker.service 
            └─2807 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```
This confirm that Docker is working on the OS.

## Steps to Deploy Docker Swarm
To ensure that Docker Swarm is running we will do the following test.
1. Create two nodes
``` Bash 
sudo nano /etc/hosts
```
And add the Ip’s of your hosts.
``` Bash 
192.168.1.10 manager
192.168.1.11 worker-1
192.168.1.12 worker-2
```
Save and close nano
2. Add a user for docker group.
``` Bash 
sudo usermod -aG docker ${USER}
```
3. Initialize docker swarm, it is very likely it will be installed, if is not installed you will need to reinstall docker.
``` Bash 
sudo docker swarm init
```
The response must looklike the following screen.
``` Bash 
Swarm initialized: current node (fsuaqqpihi2eabmmq8gldzhpv) is now a manager.
To add a worker to this swarm, run the following command:
sudo docker swarm join --token SWMTKN-1-018kvdektwa74z8fajb5c1u6jyz6qfk4ood8u4qotw7go9jj0p-cfpnh7omy86xcgoh45vau2kaj 192.168.1.10:2377
To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```
4. Add a manager running the following command:.
``` Bash 
sudo docker swarm join --token SWMTKN-1-13xo81gxpb3ttjh5e335pfrmz9fbnliikgfys7u8l4r8k4m575-2gsjwjs3y1i4kgeua2yu840hw 192.168.1.10:2377
```
The expected results after run this command is following message: 
``` Bash 
This node joined a swarm as a worker.
```
4. Verify that docker swarm cluster is up and the manager was added please run the following command:
``` Bash 
sudo docker node ls
```
The outcome of the command must be something like.
``` Bash 
ID HOSTNAME STATUS AVAILABILITY MANAGER STATUS ENGINE VERSION
etx5xc5guzftmsqx4naqwvump worker-02 Ready Active 19.03.13
xop4jvrkywz4ldsgwqmfacssc * manager Ready Active Leader 19.03.13
bghrkp7ll1b9lb0ikv8x51gzy worker-01 Ready Active 19.03.13
```
## Steps for deployment of OpenFaaS based AI Engine
0. Complete successfully previos steps or make sure that Docker Swarm is initialised.
1. Install the AI Engine version of OpenFaaS by clonning this repository. Please ensure you have the rights to clone the repository.
``` Bash 
https://github.com/Charlemagne2017/AIEngine-OpenFaaS.git 
```
2. Go on the folder of AIEngine.
3. Create username:
    ``` Bash 
    echo admin | docker secret create basic-auth-user -
    echo 12345 | docker secret create basic-auth-password -
    ```
4. Deploy the OpenFaaS stack via
	``` Bash
	docker stack deploy func -c docker-compose.yml
	```
4. Install faas-cli. Instructions [here](https://docs.openfaas.com/cli/install/)
4. Connect to Grafana and add Prometheus as a new datasource using the url [http://func_prometheus:9090](http://func_prometheus:9090).
5. To test use use python3-aiengine template from the template folder to create a new function. The python3-aiengine template contains the new, more efficient of_watchdog and boilerplate code for the monitoring.
``` Bash
faas-cli new --lang python3-aiengine <name>
```
