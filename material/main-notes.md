## How to install

https://linuxhint.com/install_docker_linux_mint/

## Layered Filesystem

The container images are composed by layers and these layers can be shared with others container images. Multiple images can share the same layers, these shared layers are read-only, when the container is created, a new layer read-write is also created for the changes on the container.

## Basic Commands

All commands start with `docker`. List:

### Query

Search for something and use the return in another command. Example: docker stop -t 0 `$(docker ps -q)`

### Containers

#### Commands

- List: -ps -a(all) -q(just the ID)
- Inspect: inspect
- Remove: rm #ID_CONTAINER
- Remove all: container prune
- Create: 
    - run #IMAGE(hello-world/ubuntu)
    - Params: 
    - -it(integrate terminals)
    - -d(distached)
    - -P(dynamic port)
    - -p(speciafy port)
    - --name
    - -e AUTHOR="Name author"
- Start: start -a(attach terminal) #ID_CONTAINER
- Stop: stop -t 2(stop time) #ID_CONTAINE
- Stop and remove: docker rm -f #ID_CONTAINE

#### Network

Docker has a default network and by default all containers are in this network. The IP is generate dynamically and It is not possible use domain names in this default network. It is necessary creates your own network and It is possible use container names as domain names.

##### Commands

- Create: network create --driver bridge your-network-name-here
- Create container with network specific: run -it --name container-name --network network-name ubuntu

### Images

#### Commands

- List: images
- Remove: rmi #ID_IMAGE

#### Creating Image

Necessary config a `Docker File`

```
FROM node:latest
LABEL MAINTAINER="Antonio Christofoletti"
ENV NODE_ENV=production
ENV PORT=3000
COPY . /var/www
WORKDIR /var/www
RUN npm install
ENTRYPOINT npm start
EXPOSE $PORT
```

Then create the image:

`docker build -f DockerFile -t antonioc/node `


#### Docker Hug

Pretty similar to git system. Commands: `docker login, docker push username/imageName, docker pull username/imageName`

### Volumes

It is possible save data outside the container in the Docker Host. These specificy directories can be shared with others containers. The volume storage is indepedent, in othor words, if the container is deleted, the volume is not removed.
- Create with random volume location: `docker run -v "/git/course-alura-docker/temp:/var/www"`;
- Create with specificy volume location: `docker run -it -v "/git/course-alura-docker/temp:/var/www" ubuntu`

#### Using apps inside container

It is possible run apps inside a container through volumes. Example:

`docker run -d -p 8080:3000 -v "/home/antonio/git/course-alura-docker/app-test:/var/www/" -w "/var/www" node npm start`



### Docker Composite

Containers manager. Example: Application with 1 container for the database, 3 container for API and 1 load balance. Handle all the commands in order to up or down all the containers can be painful, but, the Docker Composite is the solution for that.

Necessary create the DockerFiles related and a docker-composite.yml with the instructions.

Commands:
- docker-composite build
- docker-composite up -d(ditached)
- docker-composite down
- docker-composite restart
- docker-compose ps(list containers)