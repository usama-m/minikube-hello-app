# minikube-hello-app
GKE hello-app implementation on minikube using kubectl/helm.

1) Created a VM using VirtualBox with Ubuntu 20.04 Focal.
2) Installed minikube
3) Installed kubectl
4) Installed Docker
5) Manifest files have manually been verified to work:
```
$ minikube kubectl -- create -f  *filename*
```
6) Pods are up:
```
$ minikube kubectl -- get pods
NAME                                             READY   STATUS    RESTARTS   AGE
hello-app-minikube-deployment-785b474957-5sx49   1/1     Running   0          14s
```
7) All the desired services are up:
```
$ minikube kubectl -- get svc
NAME                              TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-app-minikube-loadbalancer   LoadBalancer   10.97.73.14      10.97.73.14   8080:31852/TCP   103s
hello-app-minikube-nodeport       NodePort       10.111.155.156   <none>        8080:32740/TCP   103s
helloweb-backend                  NodePort       10.104.9.88      <none>        8080:31486/TCP   103s
kubernetes                        ClusterIP      10.96.0.1        <none>        443/TCP          6m37s
 ```
8) Curl gets us the response back
```
$ curl http://10.97.73.14:8080
Hello, world!
Version: 2.0.0
Hostname: hello-app-minikube-deployment-785b474957-5sx49
```
9) Helm lint passed:
```
$ helm lint hello-app
==> Linting hello-app
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
```
11) Helm installation worked fine:
```
$ helm install hello-app ./hello-app
NAME: hello-app
LAST DEPLOYED: Thu Sep 30 14:52:19 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

