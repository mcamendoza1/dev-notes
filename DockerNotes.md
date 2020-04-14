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

 - Container connected to private virtual network <b>"bridge"</b>
 - Virtual network routes through NAT firewall on host IP 
 - All containers on virtual network can talk tp each other without <b>-p</b>
 


## Docker Image 

Reference to [images](https://docs.docker.com/engine/reference/commandline/images/).


Untag docker image
```sh 
docker rmi <REPOSITORY>:<TAG>
```

$ # show logs
$ docker container logs webhost


