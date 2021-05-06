## Docker Compose

### What is It?

Tool used to configure and manage a group of related containers. Enabling managing many containers with simples commands.

## Docker Swarm

### What is it?

Used to manage a group of docker engines as a single entity and provide orchestration (constantly trying to correct any differences between the current state and the target state)

### What Can it make?

- Handle the containers automatically;
- Define which machines have the best specifications to run specific containers;
- Handle a whole clusters of virtual or not virtual machines with containers.

## Docker Machine

### What is it?

Tool used to manage the Docker Clients in different environments. It can also create docker environments to run the applications.

Using it, It is easy to switch the Docker Host or making an upgrade.

### Concepts

- The Workers Node are responsable by running the containers
- Just the Manager Node can change the swarm state
- Tasks are related to the workers Node
- The ports are shared among the nodes and they are accessible in any swarm node.
- If It happens a problem and the last node manager goes down, The swarm will keep its current jobs, however, It will not long be possible manage the swarm, using writing and read information.

- RAFT Algorithm is used to find a new manager between the available candidates right after the previous manager went down. Rules to work:

 1. It needs to have (N/2) + 1 quorum
 2. Support (N-1)/2 fails
* N = Quantity of managers

 The docker suggest 3/5/7 managers. More than this It will interfer on the performance

- The communication among the nodes is made by a network called "ingress" that uses driver "overlay". All the communication is encrypted.

The default swarm network "ingress" only allows the communication by IP among the containers. In order to use the "Service Discovery" is necessary create another network with the driver overlay and use it on the containers that need to work with names instead of IPs. This custom network is also called "User-Defined Overlay".

The networks created on the swarm will be only visible by default for the managers. The workers only shows the networks that they uses (Lazy process). The same with the containers, they only show the informations that they use.

It is possible allow the "User-Defined Overlay" to communicate with local cotainers in a docker-machine using the flag --attachable during the network creation:

`docker network create -d overlay --attachable my_overlay`

### Commands

- docker-machine create -d virtualbox "docker-machine-name"
- docker-machine ssh "docker-machine-name"
- docker node inspect
- docker network ls

##### Inside a docker machine

- docker info

- Managers
    - docker swarm init --advertise-addr 192.168.99.102
    - docker swarm join-token worker/manager
    - docker node ls --format "{{.Hostname}} {{.ManagerStatus}}"
    - docker node demote "hash"
    - docker node rm "hash"
    - docker service create -p 8080:3000 --mode=replicated(Default)/global aluracursos/barbearia
    - docker service create --name servicoxx --network my_overlay --replicas 2 alpine sleep 1d
    - docker service ps "task"
    - docker node update --availability active/drain "vm hostname"
    - docker service update --constraint-add node.role==worker "service id"
    - docker service update --constraint-rm node.role==worker "service id"
    - docker service update --replicas "quantity replicas" "service id" or docker service scale "service hash"="quantity replicas"

    The docker manager keep the data related to the swarm in this folder: `/var/lib/docker/swarm/`. If It will be necessary to restart the node manager: `docker swarm init --force-new-cluster --advertise-addr 192.168.99.100`. It is important make backups of this folder.

- Workers
    - docker swarm join --token SWMTKN-1-32gee5uvq93zexlmk2n8fqg57quxxwt5qwssyd7ozr3j5uyy40-4bjuuv7wlcz40thsuvctu5sjf 192.168.99.102:2377
    - docker swarn leave


### Docker-composite e docker-swarm together

It is possible configure the services on the docker-composite
