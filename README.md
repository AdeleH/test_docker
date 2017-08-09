# Test Docker

A very basic simple test for Docker: build, ship, and run a very simple app.

Code and documentation from https://docs.docker.com/get-started/

## Build and run a very basic simple app 

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


## Stop the app
 
- Check out the runnning container

```
docker ps
```

- Stop the container

```
docker stop CONTAINER_ID
```


## Push the tagged image

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

=> Check out your account settings to update the default visibility of your repo.

## Pull and run the image from your remote repo

```
$> docker run -p 4000:80 adeleh/get-started:part1
```

=> N.B. if you don't specify the tag, by default **docker daemon** will pull the "latest"
