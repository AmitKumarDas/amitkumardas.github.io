---
layout: post
title: Kubernetes Travels - Understanding moving parts of Kubernetes
---

# Kubernetes Travels - Understanding moving parts of Kubernetes

> This can be thought of a book that meanders across the tool (or river) called 
Kubernetes (*of the size of Amazon*). This book has been divided across many chapters.
This chapter (i.e. Chapter 3) puts some details around various parts of Kubernetes.

## Chapter 3 - Understanding moving parts of Kubernetes

> Let me now understand the various jargons w.r.t deployment, replica set, pods, etc. This 
should give me more insights into handling applications settings, & hopefully into managing the
same applications at scale.

- http://<kubernetes-master>:8080/ui  redirects to the following URL:
  - https://<kubernetes-master>/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard

> Why ? Can we get a hang of the web architecture from this redirection ? It seems to indicate the
dashboard as a service in kube-system namespace. However, its big selling point is *`it can read all 
resources from all namespaces of kubernetes environment.`*

> Earlier, we saw that deployments made through `helm` were understood by dashboard. I am sure same 
holds true for kubectl too, i.e. If we provision or de-provision via kubectl, web dashboard will reflect
the same. It should be a stateless application.

## Notes on Label Selectors

- Not the same as names & UIDs
- Do not provide uniqueness
- We expect many objects to carry the same label(s)
- Can be comma separated values which acts as an AND logical operator
- An empty label selector selects every object in the collection
- They should not overlap within a namespace
- Various operators supported are =, ==, !=
  - environ = prod
  - tier != classA
  - type == frontend
  - type == nfs
- Set based operators like in & notin are supported
  - environ in (prod, qa)
  - tier notin (frontend, backend)
- Resources such as Job, Deployment, Replica Set & Daemon Set support set based requirements as well

## A few things about namespace

- Did you know : Typical namespaces seen in kubernetes cluster are `default`, `kube-system`.

> Cluster operators define hard resource usage limits w.r.t *Namespace*.
Can use multiple namespaces within a cluster that categorize dev, prod & qa.

- **NOTE:**
  - these services can be accessed by using their FQDN
  - service creation creates an entry in DNS of the form:
    - <service-name>.<namespace-name>.svc.cluster.local

- When we delete a namespace, it deltes everything under the namespace.
- This is asynchronous.
- Optional *`finalizers`* field:
  - allows observables to purge resources when namespace is deleted.

## Learn you some POD

> We shall start directly with a sample YAML specification

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:  # specification of the pod's contents
  restartPolicy: Never
  containers:
  - name: hello
    image: "ubuntu:14.04"
    command: ["/bin/echo", "hello", "world"]
```

- metadata.name will be the name of the pod resource
  - must be unique within the cluster
- containers[0].name is the nickname for the container within that pod
- image is the name of the Docker image
- restartPolicy: Never means we want to run container once & then terminate the pod
- command overrides the Docker container's Entrypoint

## Things to know w.r.t. Deployments

> Unlike the case where you create pods directly, `deployment` replaces pods that are deleted or
terminated for any reason (*s.a the case of node failure*)

> If you omit the replicas section it will default to a single replica

## Notes on replication controllers

- Advice: Create a rc (*alternatively viewed as Replicated Pods*) even if the application requires only a single pod.

> Replication controller supervises multiple pods across multiple nodes. An advanced form of
process supervisor (latter can only manage individual process(es) on a single node)

> Most common use-case is to run several replicas of a replicated service.

### Replication Controllers & Deletion

> Note - You can delete a replication controller without affecting any of its pods. Use
--cascade=false option to kubectl delete. Once the original is deleted, you can create a new 
replication controller to replace it. The old & new rc's `.spec.selector` should be same to 
enable the new rc adopt the old pods. 

> NOTE - There will be no effort (*automatic*) to make existing pods match a new, 
different pod template. 

### Update pod to a new spec - Rolling Update

> Note - To update pods to a new spec in a controlled way, use a `rolling update`

- Create a new rc with 1 replica
- Scale the new +1 & old -1 one by one
- Finally delete the old controller after its reaches 0 replicas
- The two rcs would need to create pods with at least one differentiating label, 
  - s.a the image tag of the primary container of the pod
  - It is typically image updates that motivate rolling updates.

### Isolating pods from a replication controller

> I shall try to give a use-case for isolating the pod from its rc. Imagine
you would want to debug a pod however without impact the production etc. So you
can go ahead and change its label. This pod will be removed & another one will be
replaced automatically associating to the rc.

> Finally, it depends. You have multiple other ways to do this as well. The entire 
thing boils down to the pod's container image(s). You may perhaps run a short job to 
do the analysis or even create a bare pod to do the analysis.

### A skeleton replication yaml

> NOTE - All kubernetes configuration files, need apiVersion, kind & metadata fields.

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

- create via kubectl
  - `kubectl create -f ./nginx-replication.yaml`
- Use the output of above command
  - kubectl describe <output>
- *`Mark the selector & labels`*
- *`The pod specification is under the template field !!`*

```bash
pods=$(kubectl get pods --selector=app=nginx --output=jsonpath={.items..metadata.name})

echo $pods
nginx-3ntk0 nginx-4ok8v nginx-qrm3m
```

## What about a job ?

- A job creates one or more pods
- It ensures that specified number of them successfully terminate
- Job tracks the successful completion
- Deleting the job will delete the pods it created

## Finally, there is a term called Daemon Set

- This ensures that **all** or **some** nodes run a copy of a pod.
- As nodes are added to the cluster, pods are added to them.
- As nodes are removed from the cluster, those pods are garbage collected.
- Deleting a Daemon Set will clean up the pods it created
  - e.g. running a node monitoring daemon on every node e.g. collectd, ganglia
  - e.g. running a node collection daemon on every node e.g. fluentd, logstash

> It is certainly possible to run daemon processes by directly starting them on a node e.g. via
init, upstartd or systemd. However, there are certain advantages to running such processes via 
a DaemonSet.

### Advantages of running Daemon Set

- running daemons in containers with resource limits increase isolation between daemons from app containers
- monitor & manage logs for daemons
- use kubectl for managing these daemons
