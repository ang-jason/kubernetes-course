# Docker demo
Docker demo files can be found and adapted from https://github.com/wardviaene/docker-demo
```
cd into the directory

docker build .

```


```
Removing intermediate container 15f6a42d3bc5
 ---> c5e3893ed93d
Successfully built c5e3893ed93d
```

```
docker run -p 3000:3000 -it c5e3893ed93d
```