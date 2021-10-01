# minikube-hello-app
GKE hello-app implementation on minikube using kubectl/helm.

1) Created a VM using VirtualBox with Ubuntu 20.04 Focal.
2) Installed minikube
3) Installed Docker
4) Firing up the cluster:
```
$ minikube start
😄  minikube v1.23.2 on Ubuntu 20.04 (vbox/amd64)
✨  Automatically selected the docker driver
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.22.2 on Docker 20.10.8 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
6) Manifest files have manually been verified to work:
```
$ minikube kubectl -- create -f  *filename*
```
5) Pods are up:
```
$ minikube kubectl -- get pods
NAME                                             READY   STATUS    RESTARTS   AGE
hello-app-minikube-deployment-785b474957-5sx49   1/1     Running   0          14s
```
6) All the desired services are up:
```
$ minikube kubectl -- get svc
NAME                              TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-app-minikube-loadbalancer   LoadBalancer   10.97.73.14      10.97.73.14   8080:31852/TCP   103s
hello-app-minikube-nodeport       NodePort       10.111.155.156   <none>        8080:32740/TCP   103s
helloweb-backend                  NodePort       10.104.9.88      <none>        8080:31486/TCP   103s
kubernetes                        ClusterIP      10.96.0.1        <none>        443/TCP          6m37s
 ```
7) Curl gets us the response back
```
$ curl http://10.97.73.14:8080
Hello, world!
Version: 2.0.0
Hostname: hello-app-minikube-deployment-785b474957-5sx49
```
8) Helm lint passed:
```
$ helm lint hello-app
==> Linting hello-app
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
```
9) Helm installation worked fine:
```
$ helm install hello-app ./hello-app
NAME: hello-app
LAST DEPLOYED: Thu Sep 30 14:52:19 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
10) Helm upgrade worked fine:
```
$ helm upgrade hello-app ./hello-app
Release "hello-app" has been upgraded. Happy Helming!
NAME: hello-app
LAST DEPLOYED: Fri Oct  1 10:46:28 2021
NAMESPACE: default
STATUS: deployed
REVISION: 3
TEST SUITE: None
```
11) Enabled the metrics server: 
```
$ minikube addons enable metrics-server
 ▪ Using image k8s.gcr.io/metrics-server/metrics-server:v0.4.2
🌟  The 'metrics-server' addon is enabled
```
12) In another tab, started sending requests for increasiong the load:
```
$ ab -c 5 -n 1000 -t 100000 http://192.168.49.2:31852/
This is ApacheBench, Version 2.3 <$Revision: 1843412 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.49.2 (be patient)
Completed 5000 requests
Completed 10000 requests
Completed 15000 requests
Completed 20000 requests
Completed 25000 requests
Completed 30000 requests
Completed 35000 requests
Completed 40000 requests
Completed 45000 requests
Completed 50000 requests
Finished 50000 requests
```
12) Increase in CPU utilization can be confirmed:
```
$ kubectl top node
NAME       CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
minikube   675m         33%    830Mi           21%       

$ kubectl top node
NAME       CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
minikube   1139m        56%    831Mi           21% 
```
