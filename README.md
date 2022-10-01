# Kubernetes course
This repository contains the course files for my Kubernetes course on Udemy: https://www.udemy.com/learn-devops-the-complete-kubernetes-course/?couponCode=KUBERNETES_GITHUB

# My Notes
Using terminal on wsl
```
sudo dockerd

docker run hello-world
```
```https://minikube.sigs.k8s.io/docs/start/```

```
minikube start


❯ minikube version
minikube version: v1.27.0
commit: 4243041b7a72319b9be7842a7d34b6767bbdac2b

```
Install kubectl
```
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

kubectl version --client --output=yaml  
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