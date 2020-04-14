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
➜  docker docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
a08e8892b2eb        bridge              bridge              local 
c79bbe6757d3        host                host                local
6c34d2544352        none                null                local
# --network bridge : docker default virtual network, NAT behind the Host ip
# --network host   : it gains performance by skipping virtual networks but sacrifices security of container model
# --network none   : removes eth0 and only leaves you with localhost interface in container
```


## Docker Image 

Reference to [images](https://docs.docker.com/engine/reference/commandline/images/).

Untag docker image
```sh 
docker rmi <REPOSITORY>:<TAG>
```


## Best Practice

### Network 
 - Container connected to private virtual network <b>"bridge"</b>
 - Virtual network routes through NAT firewall on host IP 
 - All containers on virtual network can talk tp each other without <b>-p</b>
- Create new virtual network for every application
    - Network for "web-app" for database (mysql, pg) and php/apache containers
    - Network for "api" for api framework (nodejs, spring) and datasources
