# Docker Notes

## Docker Repo 
Reference to [login](https://docs.docker.com/engine/reference/commandline/login/).

```sh
docker login --username <username> 
Password: ******  
```

### DockerHub
Login to DockerHub.  
```sh 
# your password from dockerhub
cat PASSWORD.txt | docker login --username <USERNAME> --password-stdin
```

### Github
Login to github. Make sure to generate github token. 
```sh 
# contains token from github
cat GITHUB_TOKEN.txt | docker login docker.pkg.github.com -u <GITHUB_USERNAME> --password-stdin
```

## Docker Container 
Reference to [containers]().

### Run Command 
Command to run a container 
```sh
docker run <CONTAINER_ID>
# or
docker container run <CONTAINER_ID> 
``` 

Example: 
```sh
# Running nginx (web server) 
docker container run --publish 80:80 nginx
# or 
docker container run --publish 80:80 --detach nginx
# or 
docker container run --publish 80:80 --detach --name webhost nginx 
```

### List Command
```sh 
# list all running container(s)
docker container ls
# list all running and stopped container(s) 
docker container ls -a
```

### Process Monitoring 
```sh 
docker container top     # process list in one container 
docker container inspect # details of one container config 
docker container stats   # performance stats for all containers 
docker container logs    # shows logs from a container
``` 

### Shell inside 
```sh
docker container run -it  <CONTAINER_ID> # start new container interactively 
docker container exec -it <CONTAINER_ID> # run additional command in existing container 
# -it (bash shell)  it will give you a terminal inside the running container
```

Example: 
```sh 
docker container exec -it <CONTAINER_ID> bash
# here's a sample 
docker container exec -it mysql bash
```

### Linux distro
```sh
docker pull alpine  # small footprint linux
docker pull ubuntu  
# etc, etc . . . 
```

## Docker Networks 

### Concept
```sh 
# -p (--publish)  
docker container run -p <HOST_PORT>:<CONTAINER_PORT> --name <CONTAINER ID> -d <IMAGE>
# here's an example 
docker container run -p 80:80 --name webhost -d nginx

# check container's ip address 
docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost
```

### CLI
```sh 
docker network ls               # shows networks
docker network inspect          # docker network inspect
docker network create --driver  # create a network 
docker network connect          # attach a network to container 
docker network disconnect       # detach a network from container
```
Example: 
```sh
?  docker docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
a08e8892b2eb        bridge              bridge              local 
c79bbe6757d3        host                host                local
6c34d2544352        none                null                local
# --network bridge : docker default virtual network, NAT behind the Host ip
# --network host   : it gains performance by skipping virtual networks but sacrifices security of container model
# --network none   : removes eth0 and only leaves you with localhost interface in container
```

### DNS 
```sh 
# ping NGINX_1 to NGINX_2
# make sure you attach with the same network
docker container exec -it NGINX_1 ping NGINX_2 
# this works vice versa
```

### DNS Round Robin Test
```sh
# 1. create network 
docker network create my_net1
# 2. create two containers
docker container run -d --net my_net1 --net-alias search elasticsearch:2 
docker container run -d --net my_net1 --net-alias search elasticsearch:2 
# 3. test 
docker container run --rm --net my_net1 alpine nslookup search
docker container run --rm --net my_net1 centos curl -s search:9200
```

## Docker Image 
Reference to [images](https://docs.docker.com/engine/reference/commandline/images/).

### Images 
```sh 
docker image ls    # show images 
```

### Image Layers  
To understand more see this [link](https://docs.docker.com/storage/storagedriver/).
```sh
docker image history <CONTAINER_ID>:<TAG_NAME> # show layers of changes made in image
docker image inspect <CONTAINER_ID>:<TAG_NAME> # return JSON metadata about the image
```

### Image Tagging 
```sh 
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]   # assign one or more tags to an image
docker rmi <REPOSITORY>:<TAG> # Untag docker image
```

### Image Push
```sh 
docker image push  # uploads changed layers to a image registry (default is DockerHub)
```

### Building Images
Reference to [builder](https://docs.docker.com/engine/reference/builder/).

```sh 
# -f command is used to specify a dockerfile, with an alias of --file
docker build -f some-dockerfile
```

```Dockerfile
FROM <repo>
ENV <environment variables>
RUN <run command>
EXPOSE <expose port(s)>
CMD <final command>
 ```
Build a docker image
```sh 
docker image build -t <REPOSITORY> .
```

### Extend Official Image
Example: 
```Dockerfile
FROM nginx:latest
WORKDIR /usr/share/nginx/html
COPY index.html index.html
```

### Image Prune 

```sh 
docker image prune     # to clean up just "dangling" images
docker system prune    # will clean up everything 
docker image prune -a  # which will remove all images you're not using. 
docker system df       # to see space usage
```

## Container Lifetime & Persistent Data
Reference to docker storage, see this [link](https://docs.docker.com/storage/).

### Persistent Data: Volume

```Dockerfile
FROM <repo>
ENV <environment variables>
RUN <run command>
# manual delete once created
VOLUME <volumn directory> 
EXPOSE <expose port(s)>
CMD <final command>
 ```

### Persistent Data: Bind Mounting

```sh
# Binding from host to container 
docker container ... run -v /Users/mcamendoza/my_volume:/path/container # mac & linux
docker container ... run -v //c/Users/mcamendoza/my_volume:/path/container # windows

# example 
docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html
```

```sh 
docker volume ls   # list the volumes inside your host
# create docker volume create
docker volume create --help
# example 
docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql

# -v mysql-db:/var/lib/mysql 
#      |
#      |--- volume name
```

## Best Practice
### Network 
 - Container connected to private virtual network <b>"bridge"</b>
 - Virtual network routes through NAT firewall on host IP 
 - All containers on virtual network can talk tp each other without <b>-p</b>
- Create new virtual network for every application
    - Network for "web-app" for database (mysql, pg) and php/apache containers
    - Network for "api" for api framework (nodejs, spring) and datasources
