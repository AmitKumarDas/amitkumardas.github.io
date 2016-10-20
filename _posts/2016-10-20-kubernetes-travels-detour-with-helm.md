---
layout: post
title: Kubernetes Travels - Detour with Helm
---

# Kubernetes Travels - Detour with Helm 

> This can be thought of a book that meanders across the tool (or river) called 
Kubernetes (*of the size of Amazon*). This book has been divided across many chapters.
This chapter (i.e. Chapter 2) puts some details around use of Helm.

## Chapter 2 - Detour with Helm

> I find that working with Kubernetes, means understanding various tools that helps
dealing with Kubernetes easier. One such tool is helm. It is marketed as a packing tool
for kubernetes. While I am not sure if its good over vanilla kubectl, lets have a look.
Sit tight to take a detour with helm.

```bash
amit:~$ helm init

Creating /home/amit/.helm
Creating /home/amit/.helm/repository
Creating /home/amit/.helm/repository/cache
Creating /home/amit/.helm/repository/local
Creating /home/amit/.helm/repository/repositories.yaml
Creating /home/amit/.helm/repository/local/index.yaml
$HELM_HOME has been configured at $HOME/.helm.

Tiller (the helm server side component) has been installed into your Kubernetes Cluster.
Happy Helming!
```

> I am not sure where Tiller got installed. Tried to do once again; this time using sudo.

```bash
$ sudo helm init

$HELM_HOME has been configured at $HOME/.helm.
Warning: Tiller is already installed in the cluster. (Use --client-only to suppress this message.)
Happy Helming!
```

> So Tiller seems to run. How to verify ?

```bash
$ sudo kubectl get po --namespace kube-system

NAME                             READY     STATUS    RESTARTS   AGE
tiller-deploy-2434200834-gvk9m   1/1       Running   0          9m

```

> Let us try so more helm related commands.

```bash

$ helm version

Client: &version.Version{SemVer:"v2.0.0-beta.1", GitCommit:"05c04bccae63c6392ec0cb0e27790650167279f0", GitTreeState:"clean"}
E1020 12:41:08.336306    8337 portforward.go:329] an error occurred forwarding 33701 -> 44134: 
error forwarding port 44134 to pod tiller-deploy-2434200834-gvk9m_kube-system, uid : unable to do port forwarding: socat not found.
2016/10/20 12:41:08 transport: http2Client.notifyError got notified that the client transport was broken EOF.
Error: rpc error: code = 13 desc = transport is closing

```

> Based on above error, I try to find through the helm readme sections. This is what I get.

- Once Tiller is installed, running helm version should show you both the client and server version. 
- If it shows only the client version, helm cannot yet connect to the server. 
  - Use kubectl to see if any tiller pods are running.

> In my case, kubectl shows tiller running as a pod. However, helm client to server communication seems to be broken.

```bash

# running on kubernetes master 
amit:~$ which socat
amit:~$ sudo which socat

# above command does not give any result
```

> So what exactly is socat ? What purpose does it solve ? How does Kubernetes or Helm use it ?

- The man pages says it as `two` bi-directional byte streams and transfer data between them.
- Others say, it can be used to manipulate sockets, one input & other output.
  - It can be used to setup small servers, parse data & react based on the content.
  - It can be used to redirect ports, bypass proxies, firewalls, etc.

> I go ahead & install socat in master as well as node servers.

```bash
# at master where helm is installed

$ helm version

Client: &version.Version{SemVer:"v2.0.0-beta.1", GitCommit:"05c04bccae63c6392ec0cb0e27790650167279f0", GitTreeState:"clean"}
E1020 13:34:30.123735    9882 portforward.go:329] an error occurred forwarding 44359 -> 44134:
error forwarding port 44134 to pod tiller-deploy-2434200834-gvk9m_kube-system, 
uid : unable to do port forwarding: nsenter not found.

2016/10/20 13:34:30 transport: http2Client.notifyError got notified that the client transport was broken EOF.
Error: rpc error: code = 13 desc = transport is closing
```

> Is this a different error now. It is still unable to do port forwarding. But this time it talks
about nsenter not found.

- It seems that nsenter is not enabled by default in Ubuntu 14.04.

```bash

# check the availability at /usr/local/bin/nsenter

# refer - https://gist.github.com/mbn18/0d6ff5cb217c36419661
# I had to install all the compiler stuff to compile util-linux with nsenter
# Avoid this in production !! I did it on kubernetes master host.
```

> Try once again !!

```bash
$ helm version

Client: &version.Version{SemVer:"v2.0.0-beta.1", GitCommit:"05c04bccae63c6392ec0cb0e27790650167279f0", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.0.0-beta.1", GitCommit:"05c04bccae63c6392ec0cb0e27790650167279f0", GitTreeState:"clean"}
```

> So things are working fine now. It will be good to note down the steps to uninstall Tiller. Tiller is the one
which is installed as a pod in the kubenertes cluster.

```bash
# No need to do this. Just remember !!
kubectl delete deployment tiller-deployment -n kube-system
```

> So lets start using helm.

```bash
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. â Happy Helming!â

$ helm search
NAME                    VERSION DESCRIPTION
stable/drupal           0.3.4   One of the most versatile open source content m...
stable/jenkins          0.1.1   A Jenkins Helm chart for Kubernetes.
stable/mariadb          0.5.2   Chart for MariaDB
stable/mysql            0.1.1   Chart for MySQL
stable/redmine          0.3.3   A flexible project management web application.
stable/wordpress        0.3.1   Web publishing platform for building blogs and ...
amit@kube1:~$

amit@kube1:~$ helm install stable/mysql

Fetched stable/mysql to mysql-0.1.1.tgz
NAME: loping-toad
LAST DEPLOYED: Thu Oct 20 14:54:24 2016
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Secret
NAME                TYPE      DATA      AGE
loping-toad-mysql   Opaque    2         3s

==> v1/Service
NAME                CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
loping-toad-mysql   192.16.1.5   <none>        3306/TCP   3s

==> extensions/Deployment
NAME                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
loping-toad-mysql   1         0         0            0           3s

==> v1/PersistentVolumeClaim
NAME                STATUS    VOLUME    CAPACITY   ACCESSMODES   AGE
loping-toad-mysql   Pending                                      3s

```

```bash

$ helm ls

NAME            REVISION        UPDATED                         STATUS          CHART
loping-toad     1               Thu Oct 20 14:54:24 2016        DEPLOYED        mysql-0.1.1

$ sudo kubectl get pods --all-namespaces=true

NAMESPACE     NAME                                 READY     STATUS    RESTARTS   AGE
default       loping-toad-mysql-1951360640-gxmht   0/1       Pending   0          18m
kube-system   tiller-deploy-2434200834-gvk9m       1/1       Running   0          2h

```

> Now I want see all these from an UI. I do not know how to do it in Helm.
So let me try kubectl.

```bash
$ sudo kubectl create -f https://rawgit.com/kubernetes/dashboard/master/src/deploy/kubernetes-dashboard.yaml
deployment "kubernetes-dashboard" created
service "kubernetes-dashboard" created

$ sudo kubectl get pods --all-namespaces=true
NAMESPACE     NAME                                    READY     STATUS    RESTARTS   AGE
default       loping-toad-mysql-1951360640-gxmht      0/1       Pending   0          33m
kube-system   kubernetes-dashboard-1655269645-2w9qx   1/1       Running   0          1m
kube-system   tiller-deploy-2434200834-gvk9m          1/1       Running   0          3h

```

> However, I am not able to access the web from my laptop's browser. This link https://172.16.115.2/ui
cannot be reached. Even http://172.16.115.2/ui (i.e. without ssl) gives the same result.  Lets check if there is
any specific port ?

```bash
~$ sudo kubectl config view
apiVersion: v1
clusters:
- cluster:
    insecure-skip-tls-verify: true
    server: http://172.16.115.2:8080
  name: ubuntu
contexts:
- context:
    cluster: ubuntu
    user: ubuntu
  name: ubuntu
current-context: ubuntu
kind: Config
preferences: {}
users:
- name: ubuntu
  user:
    password: xrCSSV3POZJyLTyX
    username: admin
```

> Hmmn, I try with http://172.16.115.2:8080/ui & it works !!

> However, the UI presents some errors that happened with my recent deployments. I also find yamls related to
deployment, replica sets & pods. Probably, it is time to understand these deployment files & act accordingly.

```bash
[SchedulerPredicates failed due to PersistentVolumeClaim is not bound: "loping-toad-mysql", which is unexpected., 
SchedulerPredicates failed due to PersistentVolumeClaim is not bound: "loping-toad-mysql", which is unexpected., 
SchedulerPredicates failed due to PersistentVolumeClaim is not bound: "loping-toad-mysql", which is unexpected.]
```

## References

- https://github.com/kubernetes/helm
