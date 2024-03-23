# sample-helm
Repository for exemplary code - of a working Helm Chart deployed to minikube Kubernetes local cluster.

# Execution steps
## 0. RUNNING MINIKUBE LOCAL CLUSTER ##
0.1. `minikube start`
```
😄  minikube v1.32.0 on Ubuntu 19.10
✨  Using the docker driver based on existing profile
💨  For improved Docker performance, Upgrade Docker to a newer version (Minimum recommended version is 20.10.0, minimum supported version is 18.09.0, current version is 19.03.8)
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🏃  Updating the running docker "minikube" container ...
🐳  Preparing Kubernetes v1.28.3 on Docker 24.0.7 ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass

❗  /usr/bin/kubectl is version 1.18.0, which may have incompatibilities with Kubernetes 1.28.3.
    ▪ Want kubectl v1.28.3? Try 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
0.2. `minikube dashboard`
```
🔌  Enabling dashboard ...
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

        minikube addons enable metrics-server


🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:43709/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
Opening in existing browser session.
```
0.3 `minikube addons enable ingress`
```
💡  ingress is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/ingress-nginx/controller:v1.9.4
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20231011-8b53cabe0
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20231011-8b53cabe0
🔎  Verifying ingress addon...
🌟  The 'ingress' addon is enabled
```

## 1. INSTALLING HELM CHART ##
`helm install sample-helm sample-helm/`
```
# Output:
# NAME: sample-helm
# LAST DEPLOYED: Sat Mar 23 18:07:56 2024
# NAMESPACE: default
# STATUS: deployed
# REVISION: 1
# TEST SUITE: None
```

## 2. DESCRIBING SERVICE ##
`kubectl get service my-hello-app-service`
```
# Output:
# NAME                   TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
# my-hello-app-service   NodePort   10.103.100.175   <none>        80:31528/TCP   47s
```

## 3. GETTING SERVICE IP ##
`curl $(minikube service my-hello-app-service --url)`
```
# Output:
# Hello, world!
# Version: 2.0.0
```

## 4. DESCRIBING INGRESS - CAN TAKE SOME TIME ##
`kubectl get ingress`
```
# Output:
# NAME                   CLASS   HOSTS                 ADDRESS        PORTS   AGE
# my-hello-app-ingress   nginx   my-hello-app.k8s.io   192.168.49.2   80      70s
```

## 5. VERIFYING INGRESS REDIRECTING TRAFFIC ##
`curl --resolve "my-hello-app.k8s.io:80$( minikube ip)" -i http://my-hello-app.k8s.io`
```
# Output:
#   HTTP/1.1 200 OK
#   Date: Sat, 23 Mar 2024 17:09:59 GMT
#   Content-Type: text/plain; charset=utf-8
#   Content-Length: 79
#   Connection: keep-alive
#
#   Hello, world!
#   Version: 2.0.0
#   Hostname: my-hello-app-deployment-68776d84c-vb9xb
```

## 6. VERIFYING INGRESS REDIRECTING TRAFFIC - WITH BROWSER ##
`vim /etc/hosts`
```
# add the following line: 
# <minikube_ip>    my-hello-app.k8s.io 
# e.g. 
# 192.168.49.2    my-hello-app.k8s.io
#
# check with browser, going to address: http://my-hello-app.k8s.io
```

## 7. UNINSTALLING HELM CHART ##
`helm uninstall sample-helm`