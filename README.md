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

## kind: Pod
## 01-first-app
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
minikube service -all

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


## kind: ReplicationController
## 02 - replication-controller
```
spec container > metadata > kind (ReplicationController) spec selector (app: helloworld)

kubectl get nods

kubectl create -f 02-replication-controller/helloworld-repl-controller.yml
replicationcontroller/helloworld-controller created


❯ kubectl get pods
NAME                          READY   STATUS              RESTARTS   AGE
helloworld-controller-6r526   0/1     ContainerCreating   0          23s
helloworld-controller-mbkcn   0/1     ContainerCreating   0          23s


❯ kubectl describe pod helloworld-controller-6r526

Name:             helloworld-controller-6r526
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Sun, 02 Oct 2022 12:03:29 +0800
Labels:           app=helloworld
Annotations:      <none>
Status:           Running


Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  72s   default-scheduler  Successfully assigned default/helloworld-controller-6r526 to minikube
  Normal  Pulling    70s   kubelet            Pulling image "angjason/k9s-demo"
  Normal  Pulled     24s   kubelet            Successfully pulled image "angjason/k9s-demo" in 46.687333439s
  Normal  Created    23s   kubelet            Created container k8s-demo-ja
  Normal  Started    23s   kubelet            Started container k8s-demo-ja



kubectl delete pod helloworld-controller-6r526
pod "helloworld-controller-6r526" deleted


❯ kubectl get pods
NAME                          READY   STATUS        RESTARTS   AGE
helloworld-controller-6r526   1/1     Terminating   0          2m33s
helloworld-controller-6tcmf   1/1     Running       0          9s
helloworld-controller-mbkcn   1/1     Running       0          2m33s


❯ kubectl scale --replicas=4 -f 02-replication-controller/helloworld-repl-controller.yml

replicationcontroller/helloworld-controller scaled


❯ kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
helloworld-controller-4z76j   1/1     Running   0          20s
helloworld-controller-6tcmf   1/1     Running   0          101s
helloworld-controller-m7tkz   1/1     Running   0          20s
helloworld-controller-mbkcn   1/1     Running   0          4m5s

❯ kubectl get rc
NAME                    DESIRED   CURRENT   READY   AGE
helloworld-controller   4         4         4       4m30s


❯ kubectl scale --replicas=2 rc/helloworld-controller
replicationcontroller/helloworld-controller scaled

❯ kubectl get rc
NAME                    DESIRED   CURRENT   READY   AGE
helloworld-controller   2         2         2       6m24s


❯ kubectl get pods
NAME                          READY   STATUS        RESTARTS   AGE
helloworld-controller-4z76j   1/1     Terminating   0          2m49s
helloworld-controller-6tcmf   1/1     Terminating   0          4m10s
helloworld-controller-m7tkz   1/1     Running       0          2m49s
helloworld-controller-mbkcn   1/1     Running       0          6m34s
```
### Only horizontal scale with stateless app.

```
❯ kubectl delete rc/helloworld-controller
replicationcontroller "helloworld-controller" deleted


❯ kubectl get pods
NAME                          READY   STATUS        RESTARTS   AGE
helloworld-controller-m7tkz   1/1     Terminating   0          4m24s
helloworld-controller-mbkcn   1/1     Terminating   0          8m9s
```
## kind: Deployment
### Replica Set is the next-gen Replication Controller
This Replica Set is used by Deployment object rather than Replication Controller

- With a deployment object you can:
- Create a deployment (e.g. deploying an app)
- Update a deployment (e.g. deploying a new version)
- Do rolling updates (zero downtime deployments)
- Roll back to a previous version
- Pause / Resume a deployment (e.g. to roll-out to only a certain percentage)

```
❯ kubectl create -f 03-deployment/helloworld.yml
deployment.apps/helloworld-deployment created


❯ kubectl get deployments
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment   3/3     3            3           33s

(replica sets)
❯ kubectl get rs
NAME                               DESIRED   CURRENT   READY   AGE
helloworld-deployment-779b795db5   3         3         3       64s


❯ kubectl get pods --show-labels
NAME                                     READY   STATUS    RESTARTS   AGE   LABELS
helloworld-deployment-779b795db5-4p7mx   1/1     Running   0          96s   app=helloworld,pod-template-hash=779b795db5
helloworld-deployment-779b795db5-pzjs9   1/1     Running   0          96s   app=helloworld,pod-template-hash=779b795db5
helloworld-deployment-779b795db5-z5t7d   1/1     Running   0          96s   app=helloworld,pod-template-hash=779b795db5

❯ kubectl rollout status deployment/helloworld-deployment
deployment "helloworld-deployment" successfully rolled out



❯ kubectl expose deployment helloworld-deployment --type=NodePort
service/helloworld-deployment exposed

❯ kubectl get service
NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
helloworld-deployment   NodePort    10.100.47.217   <none>        3000:31524/TCP   20s
kubernetes              ClusterIP   10.96.0.1       <none>        443/TCP          37m

❯ kubectl describe service helloworld-deployment
Name:                     helloworld-deployment
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=helloworld
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.100.47.217
IPs:                      10.100.47.217
Port:                     <unset>  3000/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  31524/TCP
Endpoints:                172.17.0.5:3000,172.17.0.6:3000,172.17.0.7:3000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>


```
❯ minikube service helloworld-deployment --url
http://127.0.0.1:35039
❗  Because you are using a Docker driver on linux, the terminal needs to be open to run it.

After changing docker image

❯ kubectl set image deployment/helloworld-deployment k8s-demo-ja=angjason/k9s-demo:v2
deployment.apps/helloworld-deployment image updated

❯ kubectl rollout status deployment/helloworld-deployment
Waiting for deployment "helloworld-deployment" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "helloworld-deployment" rollout to finish: 1 old replicas are pending termination...
deployment "helloworld-deployment" successfully rolled out


❯ curl http://127.0.0.1:35039
Hello World! huatcake v2%

```