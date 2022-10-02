# Docker demo
Docker demo files can be found and adapted from https://github.com/wardviaene/docker-demo
```
cd into the directory

docker build .
docker build . -t angjason/k9s-demo:v2

❯ docker image ls
REPOSITORY                    TAG            IMAGE ID       CREATED         SIZE
angjason/k9s-demo             v2             7883f696216c   5 seconds ago   1GB

```


```
Removing intermediate container 15f6a42d3bc5
 ---> c5e3893ed93d
Successfully built c5e3893ed93d
```

```
docker run -p 3000:3000 -it c5e3893ed93d
```

## Push docker image to docker hub
```
docker image ls

$ docker login
$ docker tag imageid your-login/docker-demo
$ docker push your-login/docker-demo

❯ docker push angjason/k9s-demo:v2
The push refers to repository [docker.io/angjason/k9s-demo]

```

Refer to Docker-Demo for more details....