# Kubernetes course
This repository contains the course files of Kubernetes course on Udemy: https://www.udemy.com/learn-devops-the-complete-kubernetes-course/?couponCode=KUBERNETES_GITHUB

Adapted from wardviaene
https://github.com/wardviaene/kubernetes-course


# If using Docker Desktop with Kubenetes enabled
```

kubectl create deployment hello-kubernetes --image=k8s.gcr.io/echoserver:1.4 --port=8080

kubectl expose deployment hello-kubernetes --type=NodePort

kubectl run -i --tty busybox --image=busybox --restart=Never -- sh

$ kubectl get service hello-kubernetes
NAME               TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-kubernetes   NodePort   10.108.173.209   <none>        8080:30149/TCP   36s


$ curl localhost:30149
CLIENT VALUES:
client_address=192.168.65.3


```

# My Notes
Using terminal on wsl
```
sudo dockerd


❯ sudo service docker start

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

## kubectl test

```
kubectl create deployment hello-kubernetes --image=k8s.gcr.io/echoserver:1.4 --port=8080

kubectl expose deployment hello-kubernetes --type=NodePort


kubectl run -i --tty busybox --image=busybox --restart=Never -- sh

❯ minikube ip

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


❯ kubectl rollout history deployment/helloworld-deployment
deployment.apps/helloworld-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

❯ kubectl create -f 03-deployment/helloworld.yml --record="remarks of CHANGE-CAUSE"

❯ kubectl rollout undo deployment/helloworld-deployment
deployment.apps/helloworld-deployment rolled back


❯ kubectl get pods
NAME                                     READY   STATUS              RESTARTS   AGE
helloworld-deployment-5984d977b5-2kb94   1/1     Running             0          4m54s
helloworld-deployment-5984d977b5-5rshq   1/1     Terminating         0          4m43s
helloworld-deployment-5984d977b5-j8s5k   1/1     Terminating         0          4m37s
helloworld-deployment-779b795db5-gslpl   0/1     ContainerCreating   0          0s
helloworld-deployment-779b795db5-p9g75   1/1     Running             0          7s
helloworld-deployment-779b795db5-vtqvs   1/1     Running             0          13s



❯ curl http://127.0.0.1:35039
Hello World! huatcake%



❯ kubectl rollout history deployment/helloworld-deployment
deployment.apps/helloworld-deployment
REVISION  CHANGE-CAUSE
2         <none>
3         <none>

❯ kubectl rollout history deployment/helloworld-deployment


❯ kubectl edit deployment/helloworld-deployment
deployment.apps/helloworld-deployment edited

revisionHistoryLimit: 10

❯ kubectl set image deployment/helloworld-deployment k8s-demo-ja=angjason/k9s-demo:latest
deployment.apps/helloworld-deployment image updated


❯ kubectl rollout history deployment/helloworld-deployment
deployment.apps/helloworld-deployment
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
4         <none>

❯ kubectl rollout undo deployment/helloworld-deployment --to-revision=3
deployment.apps/helloworld-deployment rolled back


❯ kubectl rollout history deployment/helloworld-deployment
deployment.apps/helloworld-deployment
REVISION  CHANGE-CAUSE
2         <none>
4         <none>
5         <none>

❯ curl http://127.0.0.1:35039
Hello World! huatcake%


❯ kubectl delete deployment/helloworld-deployment
deployment.apps "helloworld-deployment" deleted

❯ kubectl delete svc helloworld-deployment
service "helloworld-deployment" deleted

```

## Serivces

```
port:31001 -> type NodePort

targetPort: nodejs-port

nodePort: can dont specific by specific 31001 for easy reference

```

```
❯ kubectl get node
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   59m   v1.25.0

❯ kubectl get pods
No resources found in default namespace.


❯ kubectl create -f 01-first-app/helloworld.yml
pod/nodehelloworld.example.com created


❯ kubectl describe pod nodehelloworld.example.com
Name:             nodehelloworld.example.com
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Sun, 02 Oct 2022 12:59:25 +0800
Labels:           app=helloworld
Annotations:      <none>
Status:           Running
IP:               172.17.0.5


❯ cat 01-first-app/helloworld-nodeport-service.yml
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  ports:
  - port: 31001
    nodePort: 31001
    targetPort: nodejs-port
    protocol: TCP
  selector:
    app: helloworld
  type: NodePort

❯ kubectl create -f 01-first-app/helloworld-nodeport-service.yml
service/helloworld-service created


❯ kubectl get service
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
helloworld-service   NodePort    10.101.106.51   <none>        31081:31081/TCP   3s
kubernetes           ClusterIP   10.96.0.1       <none>        443/TCP           74m

** minikube seems to provide another port number while using docker-desktop alone will keep the defined port number**

❯ minikube service helloworld-service --url
http://127.0.0.1:33701
❗  Because you are using a Docker driver on linux, the terminal needs to be open to run it.

❯ curl http://127.0.0.1:33701
Hello World! huatcake%

You suppose to curl xxx.xxx:31081

or ipconfig 

http://192.168.50.74:31081/
```


## Labels

```
$ kubectl label nodes node1 hardware=high-spec
$ kubectl label nodes node2 hardware=low-spec


      nodeSelector:
        hardware: high-spec


❯ kubectl create -f 03-deployment/helloworld-nodeselector.yml
deployment.apps/helloworld-deployment created


❯ kubectl get deployments
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
hello-minikube          1/1     1            1           54m
helloworld-deployment   0/3     3            0           24s


❯ kubectl describe deployment helloworld-deployment
Name:                   helloworld-deployment


  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  62s   deployment-controller  Scaled up replica set helloworld-deployment-7dd7dc894 to 3


❯ kubectl get nodes --show-labels
NAME       STATUS   ROLES           AGE   VERSION   LABELS
minikube   Ready    control-plane   59m   v1.25.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=minikube,kubernetes.io/os=linux,minikube.k8s.io/commit=4243041b7a72319b9be7842a7d34b6767bbdac2b,minikube.k8s.io/name=minikube,minikube.k8s.io/primary=true,minikube.k8s.io/updated_at=2022_10_02T13_32_42_0700,minikube.k8s.io/version=v1.27.0,node-role.kubernetes.io/control-plane=,node.kubernetes.io/exclude-from-external-load-balancers=



❯ kubectl label nodes minikube hardware=high-spec
node/minikube labeled

❯ kubectl get nodes --show-labels
NAME       STATUS   ROLES           AGE   VERSION   LABELS
minikube   Ready    control-plane   61m   v1.25.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,hardware=high-spec,kubernetes.io/arch=amd64,kubernetes.io/hostname=minikube,kubernetes.io/os=linux,minikube.k8s.io/commit=4243041b7a72319b9be7842a7d34b6767bbdac2b,minikube.k8s.io/name=minikube,minikube.k8s.io/primary=true,minikube.k8s.io/updated_at=2022_10_02T13_32_42_0700,minikube.k8s.io/version=v1.27.0,node-role.kubernetes.io/control-plane=,node.kubernetes.io/exclude-from-external-load-balancers=

"hardware=high-spec"

❯ kubectl get pods
NAME                                    READY   STATUS              RESTARTS        AGE
hello-minikube-68ff6fd96-jr4qt          1/1     Running             1 (6m40s ago)   58m
helloworld-deployment-7dd7dc894-d5rzz   0/1     ContainerCreating   0               4m37s
helloworld-deployment-7dd7dc894-q4blg   0/1     ContainerCreating   0               4m37s
helloworld-deployment-7dd7dc894-qv2w6   0/1     ContainerCreating   0               4m37s


❯ kubectl get pods
NAME                                    READY   STATUS    RESTARTS   AGE
helloworld-deployment-7dd7dc894-7sc69   1/1     Running   0          103s
helloworld-deployment-7dd7dc894-d9ftx   1/1     Running   0          103s
helloworld-deployment-7dd7dc894-pwwcg   1/1     Running   0          103s





❯ kubectl describe pods
Name:             helloworld-deployment-7dd7dc894-7sc69


Events:
  Type     Reason            Age    From               Message
  ----     ------            ----   ----               -------
  Warning  FailedScheduling  2m50s  default-scheduler  0/1 nodes are available: 1 node(s) didn't match Pod's node affinity/selector. preemption: 0/1 nodes are available: 1 Preemption is not helpful for scheduling.
  Normal   Scheduled         119s   default-scheduler  Successfully assigned default/helloworld-deployment-7dd7dc894-pwwcg to minikube
  Normal   Pulling           118s   kubelet            Pulling image "angjason/k9s-demo"
  Normal   Pulled            85s    kubelet            Successfully pulled image "angjason/k9s-demo" in 32.768299335s
  Normal   Created           84s    kubelet            Created container k8s-demo-ja
  Normal   Started           84s    kubelet            Started container k8s-demo-ja


```

## Healthcheck
## 03-deployment


```
        livenessProbe:
          httpGet:
            path: /
            port: nodejs-port
          initialDelaySeconds: 15
          timeoutSeconds: 30
```

```
❯ kubectl create -f 03-deployment/helloworld-healthcheck.yml
deployment.apps/helloworld-deployment created

❯ kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
helloworld-deployment-56d7788695-kfrrp   1/1     Running   0          54s
helloworld-deployment-56d7788695-vq9lp   1/1     Running   0          54s


❯ kubectl describe pod helloworld-deployment-56d7788695-vq9lp
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  34s   default-scheduler  Successfully assigned default/helloworld-deployment-56d7788695-vq9lp to minikube
  Normal  Pulling    32s   kubelet            Pulling image "angjason/k9s-demo"
  Normal  Pulled     26s   kubelet            Successfully pulled image "angjason/k9s-demo" in 6.200603782s
  Normal  Created    26s   kubelet            Created container k8s-demo-ja
  Normal  Started    26s   kubelet            Started container k8s-demo-ja



❯ kubectl edit -f 03-deployment/helloworld-healthcheck.yml


      - image: angjason/k9s-demo
        imagePullPolicy: Always
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /
            port: nodejs-port
            scheme: HTTP
          initialDelaySeconds: 15
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 30




❯ kubectl delete --all deployments
deployment.apps "helloworld-deployment" deleted


```



## 03-deployment 
## liveness-readiness
```

❯ kubectl create -f 03-deployment/helloworld-healthcheck.yml && watch -n kubectl get pods

Every 1.0s: kubectl get pods                                                   DESKTOP-11808A8: Mon Oct  3 21:37:36 2022

NAME                                     READY   STATUS              RESTARTS   AGE
helloworld-deployment-56d7788695-f2nb5   0/1     ContainerCreating   0          2s
helloworld-deployment-56d7788695-mn8cd   0/1     ContainerCreating   0          2s

Every 1.0s: kubectl get pods                                                   DESKTOP-11808A8: Mon Oct  3 21:37:47 2022

NAME                                     READY   STATUS    RESTARTS   AGE
helloworld-deployment-56d7788695-f2nb5   1/1     Running   0          13s
helloworld-deployment-56d7788695-mn8cd   1/1     Running   0          13s


        readinessProbe:
          httpGet:
            path: /
            port: nodejs-port
          initialDelaySeconds: 15
          timeoutSeconds: 30

```

```
❯ kubectl create -f 03-deployment/helloworld-liveness-readiness.yml && watch -n kubectl get pods


Every 1.0s: kubectl get pods                                                   DESKTOP-11808A8: Mon Oct  3 21:40:59 2022

NAME                                     READY   STATUS              RESTARTS   AGE
helloworld-deployment-56d7788695-f2nb5   1/1     Running             0          3m25s
helloworld-deployment-56d7788695-mn8cd   1/1     Running             0          3m25s
helloworld-readiness-784498bcf4-487nm    0/1     ContainerCreating   0          3s
helloworld-readiness-784498bcf4-5s86s    0/1     ContainerCreating   0          3s
helloworld-readiness-784498bcf4-qg8fh    0/1     ContainerCreating   0          3s
helloworld-readiness-784498bcf4-xwkmv    0/1     ContainerCreating   0          3s

*Ready not ready 0/1 (readinessProbe)*

helloworld-readiness-784498bcf4-487nm    1/1     Running   0          29s
helloworld-readiness-784498bcf4-5s86s    0/1     Running   0          29s
helloworld-readiness-784498bcf4-qg8fh    0/1     Running   0          29s
helloworld-readiness-784498bcf4-xwkmv    0/1     Running   0          29s

```


## 04-pod-lifecycle
## Pod Lifecycle


```
❯ kubectl create -f 04-pod-lifecycle/lifecycle.yaml && watch -n kubectl get pods

or split screens to see the effect


❯ kubectl create -f 04-pod-lifecycle/lifecycle.yaml
deployment.apps/lifecycle created

Every 1.0s: kubectl get pods                                                   DESKTOP-11808A8: Mon Oct  3 21:47:53 2022

NAME                         READY   STATUS     RESTARTS   AGE
lifecycle-74c7f84c7c-7ql92   0/1     Init:0/1   0          8s

lifecycle-74c7f84c7c-7ql92   0/1     PodInitializing   0          18s

lifecycle-74c7f84c7c-7ql92   1/1     Running   0          90s


❯ kubectl exec -it lifecycle-74c7f84c7c-7ql92 -- tail /timing -f

Defaulted container "lifecycle-container-ja" out of: lifecycle-container-ja, init (init)
1664804883: Running
1664804883: postStart
1664804893: end postStart
1664804925: readinessProbe
1664804925: livenessProbe
1664804935: livenessProbe
1664804935: readinessProbe
1664804945: livenessProbe
1664804945: readinessProbe
1664804955: readinessProbe
1664804955: livenessProbe
1664804965: readinessProbe
1664804965: livenessProbe
1664804975: livenessProbe
1664804975: readinessProbe
1664804985: livenessProbe
1664804985: readinessProbe
1664804995: livenessProbe
1664804995: readinessProbe
command terminated with exit code 137

Continue as long as main container is alive

NAME                         READY   STATUS      RESTARTS       AGE
lifecycle-74c7f84c7c-7ql92   0/1     Completed   1 (2m9s ago)   4m27s



kubectl delete deployments --all
```




## 03-deployment
## Secrets / Volume


```

❯ kubectl create -f 03-deployment/helloworld-secrets.yml
secret/db-secrets created

❯ kubectl rollout restart -f 03-deployment/helloworld-secrets-volumes.yml
deployment.apps/helloworld-deployment restarted


❯ kubectl create -f 03-deployment/helloworld-secrets-volumes.yml
deployment.apps/helloworld-deployment created


❯ kubectl get pods
NAME                                     READY   STATUS              RESTARTS   AGE
helloworld-deployment-5956f7cc6c-2xqk4   0/1     ContainerCreating   0          8s
helloworld-deployment-5956f7cc6c-tpwnv   0/1     ContainerCreating   0          8s
helloworld-deployment-5956f7cc6c-xh5nw   0/1     ContainerCreating   0          8s



❯ kubectl describe pod helloworld-deployment-5c89478f75-6m45n
Name:             helloworld-deployment-5c89478f75-6m45n
    Mounts:
      /etc/creds from cred-volume (ro)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-6pzwz (ro)

Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  45s   default-scheduler  Successfully assigned default/helloworld-deployment-5c89478f75-6m45n to minikube
  Normal  Pulling    45s   kubelet            Pulling image "angjason/k9s-demo"
  Normal  Pulled     42s   kubelet            Successfully pulled image "angjason/k9s-demo" in 3.157016299s
  Normal  Created    42s   kubelet            Created container k8s-demo-ja
  Normal  Started    42s   kubelet            Started container k8s-demo-ja



❯ kubectl get secrets
NAME         TYPE     DATA   AGE
db-secrets   Opaque   2      3m53s  



❯ kubectl exec -it helloworld-deployment-5c89478f75-6m45n -- /bin/bash
root@helloworld-deployment-5c89478f75-6m45n:/app#

root@helloworld-deployment-5c89478f75-6m45n:/app# cat /etc/creds/username
rootroot@helloworld-deployment-5c89478f75-6m45n:/app# cat /etc/creds/password
passwordroot@helloworld-deployment-5c89478f75-6m45n:/app#

root@helloworld-deployment-5c89478f75-6m45n:/app# mount

tmpfs on /etc/creds type tmpfs (ro,relatime,size=12561268k)
tmpfs on /run/secrets/kubernetes.io/serviceaccount type tmpfs (ro,relatime,size=12561268k)


root@helloworld-deployment-5c89478f75-6m45n:/app# ls  /run/secrets/kubernetes.io/serviceaccount
ca.crt  namespace  token

exit
```


## 06-wordpress
## Wordpress / Wordpress secrets

```
❯ kubectl create -f 06-wordpress/wordpress-secrets.yml
secret/wordpress-secrets created


❯ kubectl create -f 06-wordpress/wordpress-single-deployment-no-volumes.yml
deployment.apps/wordpress-deployment created

❯ kubectl get pods
NAME                                    READY   STATUS              RESTARTS   AGE
wordpress-deployment-7444749967-k82f4   0/2     ContainerCreating   0          12s


❯ kubectl describe pod wordpress-deployment-7444749967-k82f4
Name:             wordpress-deployment-7444749967-k82f4


  Normal  Started    5s                kubelet            Started container mysql
  Normal  Created    4s (x2 over 32s)  kubelet            Created container wordpress
  Normal  Started    4s (x2 over 32s)  kubelet            Started container wordpress


❯ kubectl create -f 06-wordpress/wordpress-service.yml
service/wordpress-service created


❯ kubectl get svc
NAME                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
kubernetes          ClusterIP   10.96.0.1       <none>        443/TCP           31h
wordpress-service   NodePort    10.97.240.181   <none>        31001:31001/TCP   80s


❯ minikube service wordpress-service --url
http://127.0.0.1:39093
❗  Because you are using a Docker driver on linux, the terminal needs to be open to run it.


kubectl delete pod <one of the pods>

*wordpress is being restarted - as there is no volume mount - this demonstrate stateless effect*

❯ kubectl delete deployments --all
deployment.apps "wordpress-deployment" deleted


❯ kubectl delete svc --all
service "kubernetes" deleted
service "wordpress-service" deleted


❯ kubectl delete secrets --all
secret "db-secrets" deleted
secret "wordpress-secrets" deleted
```