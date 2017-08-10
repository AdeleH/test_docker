# Test Docker

A very basic simple test for Docker: build, ship, and run a very simple app.

Code and documentation from https://docs.docker.com/get-started/

## 1°) Build and run a very basic simple app 

- Build the app

```
$> docker build -t friendlyhello .
```

- Check out the image you just created

```
$> docker images
```

- Run the app 

```
$> docker run -p 4000:80 friendlyhello
```

=> on the `Dockerfile` the "`EXPOSE`" port is 80 but port 80 has been mapped to port 4000 ("published" port)

- Check out the result page on http://localhost:4000


## 2°) Stop the app
 
- Check out the runnning container

```
docker ps
```

- 3°) Stop the container

```
docker stop CONTAINER_ID
```


## 4°) Push the tagged image

- Create the new tagged image

```
$> docker tag friendlyhello adeleh/get-started:part1
```

- Log in

```
$> docker login
```

- Push the image

```
$> docker push adeleh/get-started:part1
```

Image pushed on: https://hub.docker.com/r/adeleh/get-started/

=> Check out your account settings to update the default visibility of your repo.


## 5°) Pull and run the image from your remote repo

```
$> docker run -p 4000:80 adeleh/get-started:part1
```

=> N.B. if you don't specify the tag, by default **docker daemon** will pull the "latest"


## 6°) Scale the app

- Run five instances of the image as one **service** called "web"

```
$> docker-compose up
```

or use the swarm mode

```
$> docker swarm init  # enable swarm mode and make your current machine a swarm manager

$> docker stack deploy --compose-file docker-compose.yml getstartedlab
```

- Check out the 5 containers running

```
$> docker stack ps getstartedlab
```

- Hit http://localhost:80 several times and verify that the container ID is different each time (**load-balancing** between containers)

## 7°) Stop the service

- Take down the app

```
$> docker stack rm getstartedlab

$> docker stack ls  # should be empty
```

- Take down the swarm

```
$> docker swarm leave --force

$> docker node ls  # should be empty 
```

## 8°) Swarm clusters

- Create a cluster with 2 nodes (VMs), 1 swarm manager (myvm1) and 1 worker (myvm2)

```
$> docker-machine create --driver virtualbox myvm1  # create VM #1

$> docker-machine create --driver virtualbox myvm2  # create VM #2

$> docker-machine ssh myvm1 "docker swarm init"  # use VM #1 as swarm manager

or $> docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.100:2377" 

$> docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-43w0xxeifkvx0awad7zl7eeioe1qy0xrcj5ie2e7sia75oy4mm-alyuiixa0r961f8xzw13lw8cp 192.168.99.100:2377"  # tell VM #2 to join the swarm

$> docker-machine ssh myvm1 "docker node ls"  # check out the list of nodes of the swarm managed by VM #1
```

- Deploy the app on the cluster

```
$> docker-machine scp docker-compose.yml myvm1:~   # copy the docker-compose.yml file to the swarm manager (home directory)
```

```
$> docker-machine ssh myvm1 "docker stack deploy -c docker-compose.yml getstartedlab"   # deploy the app on the cluster
```

The app has been deployed on the cluster.

```
$> docker-machine ssh myvm1 "docker stack ps getstartedlab"  # show the distributed containers between the 2 nodes of the cluster
```

Go to 192.168.99.100:80 and 192.168.99.101:80

## 9°) Cleanup

- Remove the app 

```
$> docker-machine ssh myvm1 "docker stack rm getstartedlab"
```

- Remove the swarm

```
$> docker-machine ssh myvm2 "docker swarm leave"

$> docker-machine ssh myvm1 "docker swarm leave --force"
```

- Stop all running VMs

```
$> docker-machine stop $(docker-machine ls -q)
```



