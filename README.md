# Kubernetes course
This repository contains the course files of Kubernetes course on Udemy: https://www.udemy.com/learn-devops-the-complete-kubernetes-course/?couponCode=KUBERNETES_GITHUB

Adapted from wardviaene
https://github.com/wardviaene/kubernetes-course

# My Notes
Using terminal on wsl
```
sudo dockerd

docker run hello-world
```
```https://minikube.sigs.k8s.io/docs/start/```

```
minikube start
```

![](https://i.imgur.com/RJ4F17x.png)
```
❯ minikube version
minikube version: v1.27.0
commit: 4243041b7a72319b9be7842a7d34b6767bbdac2b

```
Install kubectl
```
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

kubectl version --client --output=yaml  
```

```
kubectl config get-contexts


kubectl config use-context <name of env>
```


Check status of minikube clusters
```
kubectl get po -A

minikube kubectl -- get po -A



Should Return
NAMESPACE              NAME                                        READY   STATUS    RESTARTS      AGE
kube-system            coredns-565d847f94-2fbhg                    1/1     Running   3 (91s ago)   104m
kube-system            etcd-minikube                               1/1     Running   3 (91s ago)   104m
kube-system            kube-apiserver-minikube                     1/1     Running   2 (91s ago)   104m
kube-system            kube-controller-manager-minikube            1/1     Running   3 (91s ago)   104m
kube-system            kube-proxy-8qstx                            1/1     Running   2 (91s ago)   104m
kube-system            kube-scheduler-minikube                     1/1     Running   3 (91s ago)   104m
kube-system            storage-provisioner                         1/1     Running   5 (37s ago)   104m
kubernetes-dashboard   dashboard-metrics-scraper-b74747df5-5q28j   1/1     Running   3 (91s ago)   96m
kubernetes-dashboard   kubernetes-dashboard-54596f475f-s8jgc       1/1     Running   4 (37s ago)   96m
```



```
kubectl get node

❯ kubectl get node
NAME       STATUS   ROLES           AGE    VERSION
minikube   Ready    control-plane   120m   v1.25.0
```


```
kubectl create -f 01-first-app/helloworld.yml


❯ kubectl get pod  
NAME                         READY   STATUS              RESTARTS   AGE
nodehelloworld.example.com   0/1     ContainerCreating   0          28s

```

```
kubectl describe pod

kubectl describe pod nodehelloworld.example.com

```


```
kubectl port-forward nodehelloworld.example.com 8081:3000
Forwarding from 127.0.0.1:8081 -> 3000
Forwarding from [::1]:8081 -> 3000
Handling connection for 8081

curl localhost:8081
```

```
kubectl expose pod nodehelloworld.example.com --type=NodePort --name nodehelloworld-service

service/nodehelloworld-service exposed
```


## To get IP Address of the Cluster
```
minikube service nodehelloworld-service --url

http://127.0.0.1:39229
❗  Because you are using a Docker driver on linux, the terminal needs to be open to run it.


minikube service nodehelloworld-service

|-----------|------------------------|-------------|---------------------------|
| NAMESPACE |          NAME          | TARGET PORT |            URL            |
|-----------|------------------------|-------------|---------------------------|
| default   | nodehelloworld-service |        3000 | http://192.168.49.2:32389 |
|-----------|------------------------|-------------|---------------------------|
🏃  Starting tunnel for service nodehelloworld-service.
|-----------|------------------------|-------------|------------------------|
| NAMESPACE |          NAME          | TARGET PORT |          URL           |
|-----------|------------------------|-------------|------------------------|
| default   | nodehelloworld-service |             | http://127.0.0.1:38437 |
|-----------|------------------------|-------------|------------------------|
🎉  Opening service default/nodehelloworld-service in default browser...
👉  http://127.0.0.1:38437
❗  Because you are using a Docker driver on linux, the terminal needs to be open to run it.

```


```
❯ kubectl get service
NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes               ClusterIP   10.96.0.1        <none>        443/TCP          148m
nodehelloworld-service   NodePort    10.111.226.222   <none>        3000:32389/TCP   6m34s
```


```
kubectl get pods

kubectl attach nodehelloworld.example.com -i
kubectl attach busybox -i


kubectl exec nodehelloworld.example.com -- ls /app

Dockerfile
README.md
docker-compose.yml
index-db.js
index.js
misc
node_modules
package-lock.json
package.json
test



kubectl exec nodehelloworld.example.com -- touch /app/test.txt
kubectl exec nodehelloworld.example.com -- ls /app

misc
node_modules
package-lock.json
package.json
test
test.txt
```



```
❯ minikube service nodehelloworld-service --url
http://127.0.0.1:46285
❗  Because you are using a Docker driver on linux, the terminal needs to be open to run it.


❯ kubectl describe service nodehelloworld-service
Name:                     nodehelloworld-service
Namespace:                default
Labels:                   app=helloworld
Annotations:              <none>
Selector:                 app=helloworld
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.111.226.222
IPs:                      10.111.226.222
Port:                     <unset>  3000/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  32389/TCP
Endpoints:                172.17.0.5:3000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

```

## Debugging
```
kubectl run -i --tty busybox --image=busybox
--restart=Never -- sh

ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var

/ # telnet 172.17.0.5 3000
Connected to 172.17.0.5:3000

GET /
```