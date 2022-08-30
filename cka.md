# Certified Kubernetes Administrator (CKA) with Practice Tests

## CORE CONCEPTS, PRACTICE TEST – PODS

### PODs with YAML
```
kubectl run nginx --image=nginx

kubectl describe pod newpods-<id>

kubectl get pods -o wide
```

What does the READY column in the output of the kubectl get pods command indicate?
- Running Conatiners in POD/Total Conatiners in POD

```
kubectl delete pod webapp
kubectl run redis --image=redis123 --dry-run=client -o yaml > redis-definition.yaml
```

```
# redist-definition.yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis123    
```

- vi find and replace: `:s/redis123/redis/g`

### ReplicaSets
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: mysqpp
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx
 replicas: 3
 selector: 
   matchLabels:
     type: front-end
```

- ReplicaSets에서는 `selector`를 통해서 대상 `Pod` 선정
- Scale

```
kubectl replace -f replicaset-defnition.yml
kubectl scale --replicas=6 -f replicaset-definition.yml
kubectl scale --replicas=6 replicaset myapp-replicaset
```

```
kubectl get replicasets

kubectl describe replicasets new-replica-set
```

Why are there still 4 PODs, even after you deleted one?
- ReplicaSet ensures that desired number of PODs always run

Create a ReplicaSet using the `replicaset-definition-1.yaml` file located at /root/. There is an issue with the file, so try to fix it.
- `kubectl explain replicaset | grep VERSION`


```
$ kubectl explain replicaset
KIND:     ReplicaSet
VERSION:  apps/v1
```

```
kubectl delete replicaset replicaset-1

kubectl edit replicaset new-replica-set

# 기존 Pod를 삭제해야 새로운 Pod들이 실행됩니다.
kubectl delete pod new-replica-set-nxbh6 new-replica-set-62524 new-replica-set-5mv75 new-replica-set-rvpp8

# replicaset 개수 조정 (UP/DOWN)
kubectl edit replicaset new-replica-set
```

### Certification Tip!
```
kubectl run nginx --image=nginx --dry-run=client -o yaml

kubectl create deployment --image=nginx nginx --dry-run=client -o yaml

kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml

kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml
```

### Deployments
```
$ kubectl get all

$ kubectl create -f deployment-definition-1.yaml 
Error from server (BadRequest): error when creating "deployment-definition-1.yaml": deployment in version "v1" cannot be handled as a Deployment: no kind "deployment" is registered for version "apps/v1" in scheme "k8s.io/apimachinery@v1.23.3-k3s1/pkg/runtime/scheme.go:100"

$ kubectl explain deployment 
KIND:     Deployment
VERSION:  apps/v1

$ kubectl create deployment --image=httpd:2.4-alpine httpd-frontend --replicas=3 --dry-run=client -o yaml > deployment-definition.yaml
```

### Service
```
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  ports:
   - targetPort: 8080
     port: 8080
     nodePort: 30080
  selector:
    name: simple-webapp
```

### Namespaces

mysql.connect("db-service.dev.svc.cluster.local")
- cluster.local: domain
- svc: object
- dev: namespace
- db-service: service name

```
# namespace-dev.yaml

apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

```
kubectl get pods --namespace=prod
kubectl config set-context $(kubectl config current-context) --namespace=dev
kubectl get pods --all-namespaces
kubectl get po -A
```

#### Resource Quota
```
# compute-quota.yaml

apiVersion: v1
kine: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memeory: 10Gi
```

```
k get po -n research
kubectl run redis --image=redis -n finance --dry-run=client -o yaml

k get po --all-namespaces | grep blue
```

### Imperative vs Declarative

**Imperative**
```
kubectl run --image=nginx nginx
kubectl create deployment --image=nginx nginx
kubectl expose deployment nginx --port 80
kubectl edit deployment nginx
kubectl scale deployment nginx --replicas=5
kubectcl set image deployment nginx nginx=nginx:1.18
kubectl create -f nginx.yaml
kubectl replace -f nginx.yaml
kubectl delete -f nginx.yaml
```

**Declarative**
```
kubectl apply -f nginx.yaml
```

#### Certification Tips - Imperative Commands with Kubectl

```
# POD
kubectl run nginx --image=nginx --dry-run=client -o yaml

# Deployment
kubectl create deployment nginx --image=nginx --replicas=4 --dry-run=client -o yaml

kubectl create deployment nginx --image=nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml

# Service
# Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379
kubectl expose pod redis --port 6379 --name redis-service --dry-run=client -o yaml 
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml

# Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:
kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml
```

#### Practice
```
kubectl run redis --image=redis:alpine --dry-run=client -o yaml > redis-pod.yaml
kubeclt run redis --image=reids:aplpine --labels="tier=db"

kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml > redis-service.yaml

kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3 

kubectl run custom-nginx --image=nginx --port=8080

kubectl create namespace dev-ns

kubectl create deployment redis-deploy --image=redis --namespace=dev-ns --replicas=2

kubectl run httpd --image=httpd:alpine --port=80 --expose
```

## Section 3: Scheduling

### Manual Scheduling
```
# Pod-bind-definition.yaml
apiVersin: v1
kind: Binding
metadata:
  name: nginx
target:
  apiVersion: v1
  kind: Node
  name: node2

curl --header "Content-Type:application/json" --request POST --data '{"apiVersion":"v1", "kind:"Binding", ..}' http://$SERVER/api/v1/namespace/default/pods/$PODNAME/binding/
```

#### Practice

```
# Check scheduling controller
kubectl get pods --namespace kube-system

# Assign pod to node01 manually
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx
  nodeName: node01

# You should delete or replace pod first
kubectl replace --force -f nginx.yaml

# Monitor the pod you deployed
kubectl get pods --watch
kubectl get pods -o wide
```

### Labels and Selectors
```
kubectl get pods --selector app=App1
```
#### practice
```
kubectl get pods --selector env=dev
kubectl get pods --selector env=dev --no-headers | wc -l

kubectl get all --selector env=prod
kubectl get pods --selector env=prod,bu=finance,tier=frontend
```

### Taints and Tolerations

`kubectl taint nodes node-name key=value:taint-effect`
- taint-effect
  - NoSchedule
  - PreferNoSchedule
  - NoExcute

```
kubectl taint nodes node1 app=blue:NoSchedule
```

#### Practice
```
k describe  node node01 | grep Taint
kubectl taint nodes node01 spray=mortein:NoSchedule

k describe  node node01 | grep Taint
Taints:             spray=mortein:NoSchedule

kubectl run mosquito --image=nginx

```


**pod-definition.yml**
```yaml
apiVersion:
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
  
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "blue"
    effect: "NoSchdule"
```

**Master Node**
```
kubectl describe node kubemaster | grep Taint
Taints:             node-role.kubernetes.io/master:NoSchedule
```

**pod-bee.yaml**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee

  tolerations:
  - key: spray
    operator: Equal
    value: mortein
    effect: NoSchedule
```

**taints on controlplane**
```
k describe node controlplane | grep Taints
Taints:             node-role.kubernetes.io/master:NoSchedule
```

**Untaint controlplane**
```
kubectl taint nodes controlplane node-role.kubernetes.io/master:NoSchedule-
node/controlplane untainted
```

### Node Selectors

```
kubectl label nodes node01 size=large
```

### Node Affinity

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: In
            values:
            - Large
            - Medium    
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: NotIn
            values:
            - Small    
```

**Node Affinity Types**
- requiredDuringSchedulingIgnoredDuringExecution
- preferedDuringSchedulingIgnoredDuringExecution
- requiredDuringSchedulingrequiredDuringExecution

#### Practice
```
k describe node controlplane | grep -i taints
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: blue
  name: blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: blue
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: blue
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: color
                operator: In
                values:
                - blue
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: red
  name: red
spec:
  replicas: 2
  selector:
    matchLabels:
      app: red
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: red
    spec:
      affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: node-role.kubernetes.io/master
                    operator: Exists
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

### Resource Requirements and Limits

**Note on default resource requirements and limits**
```
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
    defaultRequest:
      memory: 256Mi
    type: Container
```

```
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    type: Container
```

### A quick note on editing PODs and Deployments

Remember, you CANNOT edit specifications of an existing POD other than the below.
- spec.containers[*].image
- spec.initContainers[*].image
- spec.activeDeadlineSeconds
- spec.tolerations

**1. Run the kubectl edit pod <pod name> command.**
```
kubectl delete pod webapp
kubectl create -f /tmp/kubectl-edit-ccvrq.yaml
``` 
**2. The second option is to extract the pod definition in YAML format to a file using the command**
```
kubectl get pod webapp -o yaml > my-new-pod.yaml
vi my-new-pod.yaml
kubectl delete pod webapp
kubectl create -f my-new-pod.yaml
```

### Practice

**OOMKilled**
```
$ k describe po elephant | grep -A5 State
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    1
      Started:      Mon, 15 Aug 2022 00:51:42 +0000
      Finished:     Mon, 15 Aug 2022 00:51:42 +0000
    Ready:          False
```

**watch command to get the output every two seconds**
```
watch kubectl get pods
```

**Increase the limit of the elephant pod to 20Mi.**
```
# Solution A
kubectl edit pod elephant
kubectl replace --forece -f /tmp/kubectl-edit-000000.yaml

# Solution B
kubectl get pod elephant  -o yaml > elephant.yaml
kubectl delete pod elephant
kubectl create -f elephant.yaml

```

### DaemonSet
```
kubectl get daemonsets
kubectl describe daemonsets monitoring-daemon
```

#### Practice Test

```
k get daemonsets --all-namespaces
k get daemonsets -A

k describe daemonsets kube-flannel-ds -n kube-system
k describe ds kube-flannel-ds -n kube-system
k get ds -n kube-system
```

**Deploy a DaemonSet for FluentD Logging**
An easy way to create a DaemonSet is to first generate a YAML file for a Deployment with the command `kubectl create deployment elasticsearch --image=k8s.gcr.io/fluentd-elasticsearch:1.20 -n kube-system --dry-run=client -o yaml > fluentd.yaml`. Next, remove the replicas, strategy and status fields from the YAML file using a text editor. Also, change the kind from `Deployment to DaemonSet`.

Finally, create the Daemonset by running `kubectl create -f fluentd.yaml`

### Static Pods
Static Pods are managed directly by the kubelet daemon on a specific node, without the API server observing them. Unlike Pods that are managed by the control plane

1. How many static pods exist in this cluster in all namespaces?
- Run the command `kubectl get pods --all-namespaces` and look for those with `-controlplane` appended in the name
- `ownerReference`가 `Node`인지 확인

2. What is the path of the directory holding the static pod definition files?
- `/var/lib/kubelet/config.yaml`에서 `staticPodPath` 확인: `/etc/kubernetes/manifests`

```
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep image
    image: k8s.gcr.io/kube-apiserver:v1.23.0
    imagePullPolicy: IfNotPresent
```

3. Create a static pod named `static-busybox` that uses the `busybox` image and the command `sleep 1000`
```
k run static-busybox --image=busybox --dry-run=client -o yaml > static-busybox.yaml

# static-busybox.yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-busybox
spec:
  containers:
  - image: busybox
    name: static-busybox
    command: ["sleep"]
    args: ["1000"]
```

```bash
root@controlplane:~# kubectl run static-busybox --image=busybox --restart=Never --dry-run=client -o yaml --command -- sleep 10000
```

4. We just created a new static pod named static-greenbox. Find it and delete it. This question is a bit tricky. But if you use the knowledge you gained in the previous questions in this lab, you should be able to find the answer to it.

Identify which node the static pod is created on, ssh to the node and delete the pod definition file.
If you don't know the IP of the node, run the `kubectl get nodes -o wide` command and identify the IP.
Then, SSH to the node using that IP. For static pod manifest path look at the file `/var/lib/kubelet/config.yaml` on node01

First, let's identify the node in which the pod called static-greenbox is created. To do this, run:
```bash
root@controlplane:~# kubectl get pods --all-namespaces -o wide  | grep static-greenbox
default       static-greenbox-node01                 1/1     Running   0          19s     10.244.1.2   node01       <none>           <none>
root@controlplane:~#
```

From the result of this command, we can see that the pod is running on node01.  
Next, SSH to `node01` and identify the path configured for static pods in this node.  
`Important`: The path need not be `/etc/kubernetes/manifests`.  
Make sure to check the path configured in the kubelet configuration file.

```bash
root@controlplane:~# ssh node01 
root@node01:~# ps -ef |  grep /usr/bin/kubelet 
root       752   654  0 00:30 pts/0    00:00:00 grep --color=auto /usr/bin/kubelet
root     28567     1  0 00:22 ?        00:00:11 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.2
root@node01:~# grep -i staticpod /var/lib/kubelet/config.yaml
staticPodPath: /etc/just-to-mess-with-you
root@node01:~# 
```
Here the staticPodPath is `/etc/just-to-mess-with-you`  
Navigate to this directory and delete the YAML file:

```bash
root@node01:/etc/just-to-mess-with-you# ls
greenbox.yaml
root@node01:/etc/just-to-mess-with-you# rm -rf greenbox.yaml 
root@node01:/etc/just-to-mess-with-you#
```

Exit out of node01 using CTRL + D or type exit. You should return to the controlplane node. 
Check if the static-greenbox pod has been deleted:

### Multiple Schedulers

1. What is the name of the POD that deploys the default kubernetes scheduler in this environment?
```bash
root@controlplane ~ ➜  k get po -n kube-system
```

2. Create a configmap with name `my-scheduler-config` using the content of file `/root/my-scheduler-config.yaml`
```bash
root@controlplane ~ ➜  k create -n kube-system configmap my-scheduler-config --from-file=/root/my-scheduler-config.yaml
```

3. Deploy an additional scheduler to the cluster following the given specification. Use the manifest file provided at /root/my-scheduler.yaml. Use the same image as used by the default kubernetes scheduler.

```yaml
# /root/my-scheduler.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-scheduler
  name: my-scheduler
  namespace: kube-system
spec:
  serviceAccountName: my-scheduler
  containers:
  - command:
    - /usr/local/bin/kube-scheduler
    - --config=/etc/kubernetes/my-scheduler/my-scheduler-config.yaml
    image: k8s.gcr.io/kube-scheduler:v1.23.0
    livenessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
      initialDelaySeconds: 15
    name: kube-second-scheduler
    readinessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
    resources:
      requests:
        cpu: '0.1'
    securityContext:
      privileged: false
    volumeMounts:
      - name: config-volume
        mountPath: /etc/kubernetes/my-scheduler
  hostNetwork: false
  hostPID: false
  volumes:
    - name: config-volume
      configMap:
        name: my-scheduler-config
```

4. A POD definition file is given. Use it to create a POD with the new custom scheduler. File is located at /root/nginx-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  schedulerName: my-scheduler
  containers:
  - image: nginx
    name: nginx
```

## 4. Logging & Monitoring
### Monitor Cluster Components

1. Let us deploy metrics-server to monitor the PODs and Nodes. Pull the git repository for the deployment files.

```bash
# git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git

# k create -f .    
```

2. Identify the node that consumes the most CPU.
```bash
# k top node
NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
controlplane   326m         0%     1204Mi          0%        
node01         32m          0%     345Mi           0%        
```

3. Identify the POD that consumes the most Memory.
```bash
# k top po
NAME       CPU(cores)   MEMORY(bytes)   
elephant   20m          32Mi            
lion       1m           18Mi            
rabbit     139m         252Mi           
```

### Managing Application Logs
1. A user - USER5 - has expressed concerns accessing the application. Identify the cause of the issue.
```bash
# k logs webapp-1
```

2. A user is reporting issues while trying to purchase an item. Identify the user and the cause of the issue.
```bash
# k get po webapp-2 --watch
NAME       READY   STATUS    RESTARTS   AGE
webapp-2   2/2     Running   0          33s

# k logs webapp-2 
error: a container name must be specified for pod webapp-2, choose one of: [simple-webapp db]

# k logs webapp-2 simple-webapp
...
[2022-08-24 00:56:01,413] WARNING in event-simulator: USER30 Order failed as the item is OUT OF STOCK.
...
```

## 5. Application Lifecycle Management
### Rolling Updates and Rollbacks

1. Inspect the deployment and identify the current strategy

```bash
# k get all
NAME                            READY   STATUS    RESTARTS   AGE
pod/frontend-5c74c57d95-mhqnd   1/1     Running   0          3m36s
pod/frontend-5c74c57d95-wpfnv   1/1     Running   0          3m36s
pod/frontend-5c74c57d95-srbbd   1/1     Running   0          3m36s
pod/frontend-5c74c57d95-9qzj9   1/1     Running   0          3m36s
...

# k describe deploy frontend
...
StrategyType:           RollingUpdate
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  name=webapp
  Containers:
   simple-webapp:
    Image:        kodekloud/webapp-color:v1
...
```

2. Let us try that. Upgrade the application by setting the image on the deployment to `kodekloud/webapp-color:v2`
```bash
# k edit deploy frontend
```

3. k edit deploy frontend
```yaml
...
spec:
  ...
  strategy:
    type: Recreate
```

### Commands and Arguments
1. Create a pod with the ubuntu image to run a container to sleep for 5000 seconds. Modify the file ubuntu-sleeper-2.yaml
```yaml
apiVersion: v1
kind: Pod 
metadata:
  name: ubuntu-sleeper-2
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["sleep"]
    args: ["5000"]
```

```yaml
apiVersion: v1
kind: Pod 
metadata:
  name: ubuntu-sleeper-3
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
      - "sleep"
      - "1200"
```

### Configure Environment Variables 
1. Update the environment variable on the POD to display a `green` background

```bash
# k edit po webapp-color
# k replace --force -f /tmp/kubectl-edit-000000000.yaml
```


2. Create a new ConfigMap for the `webapp-color` POD. Use the spec given below.
```bash
# kubectl create configmap webapp-config-map --from-literal=APP_COLOR=darkblue
```

```yaml
# webapp-config-map.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: webapp-config-map
data:
  APP_COLOR: "darkblue"
```

```bash
#  k get cm 
NAME                DATA   AGE
kube-root-ca.crt    1      20m
db-config           3      8m28s
webapp-config-map   1      31s
```

```yaml
  containers:
  - envFrom:
    - configMapRef:
        name: webapp-config-map
```

### Configure Secrets in Applications

**A note about Secrets**  
Secrets are not encrypted, so it is not safer in that sense. However, some best practices around using secrets make it safer. As in best practices like:
- Not checking-in secret object definition files to source code repositories.
- [Enabling Encryption at Rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) for Secrets so they are stored encrypted in ETCD. 

Also the way kubernetes handles secrets. Such as:
- A secret is only sent to a node if a pod on that node requires it.
- Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
- Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.

1. How many secrets are defined in the default-token secret?

There are three secrets - ca.crt, namespace and token.
```
Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     570 bytes
namespace:  7 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Ii1fY2MzNjQxUmhWYlFpTW5DZzJ2NHJoR25XLUxpYmZpcF9xNkF6VlZ1RDgifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3B
```

2. The reason the application is failed is because we have not created the secrets yet. Create a new secret named db-secret with the data given below.

```bash
# k create secret --help
# k create secret generic --help

# k create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123
```

```yaml
apiVersion: v1
data:
  DB_Host: c3FsMDE=
  DB_Password: cGFzc3dvcmQxMjM=
  DB_User: cm9vdA==
kind: Secret
metadata:
  name: db-secret
type: Opaque
```

3. Configure `webapp-pod` to load environment variables from the newly created secret. Delete and recreate the pod if required.

```yaml
containers:
  - name: webapp
    image: kodekloud/simple-webapp-mysql
    envFrom:
    - secretRef:
        name: db-secret
```

```bash
# k edit po webapp-pod
error: pods "webapp-pod" is invalid
A copy of your changes has been stored to "/tmp/kubectl-edit-1890615175.yaml"
error: Edit cancelled, no valid changes were saved.

# k replace --force -f /tmp/kubectl-edit-1890615175.yaml
```

### Multi Container PODs
1. Create a multi-container pod with 2 containers.

```txt
    State:          Waiting
      Reason:       CrashLoopBackOff
```

```
apiVersion: v1
kind: Pod
metadata:
  name: yellow
spec:
  containers:
  - image: busybox
    name: lemon
    command:
      - "sleep"     
      - "1000"     
  - image: redis
    name: gold  
```

```bash
# k replace --force -f yellow.yaml 
pod "yellow" deleted
pod/yellow replaced
```

2. Configure a sidecar container for the application to send logs to Elastic Search. 
```bash
# kubectl -n elastic-stack logs kibana
```

3. The application outputs logs to the file /log/app.log. View the logs and try to identify the user having issues with Login

```bash
# kubectl -n elastic-stack exec -it app -- cat /log/app.log
```

4. Edit the pod to add a sidecar container to send logs to Elastic Search. Mount the log volume to the sidecar container. Only add a new container. Do not modify anything else. Use the spec provided below.

![sidecar container](images/kubernetes-ckad-elastic-stack-sidecar_l4d1ga.png)  

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
  namespace: elastic-stack
  labels:
    name: app
spec:
  containers:
  - name: app
    image: kodekloud/event-simulator
    volumeMounts:
    - mountPath: /log
      name: log-volume

  - name: sidecar
    image: kodekloud/filebeat-configured
    volumeMounts:
    - mountPath: /var/log/event-simulator/
      name: log-volume

  volumes:
  - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: DirectoryOrCreate
```

5. Inspect the Kibana UI. You should now see logs appearing in the Discover section.

### InitContainers

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'git clone <some-repository-that-will-be-used-by-application> ; done;']
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
```


1. What is the state of the initContainer on pod blue

```yaml
Init Containers:
  init-myservice:
    ...
    State:          Terminated
```

2. Update the pod red to use an `initContainer` that uses the `busybox` image and `sleeps for 20` seconds
```yaml
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'sleep 20']
```

3. A new application `orange` is deployed. There is something wrong with it. Identify and fix the issue.

```bash
# k logs orange -c init-myservice
```

## 6. Cluster Maintenance
### OS Upgrade
1. We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.

```bash
# kubectl drain node01 --ignore-daemonsets
```

2. The maintenance tasks have been completed. Configure the node node01 to be schedulable again.

```bash
# kubectl uncordon node01
```

3. Why are the pods placed on the controlplane node?

```bash
# k describe node controlplane | grep -i taints
Taints:             <none>
```

```bash
# k drain node01 --ignore-daemonsets
node/node01 cordoned
error: unable to drain node "node01" due to error:cannot delete Pods not managed by ReplicationController, ReplicaSet, Job, DaemonSet or StatefulSet (use --force to override): default/hr-app, continuing command...
```

The drain command will not work in this case. To forcefully drain the node we now have to use the `--force` flag.  

```bash
# k drain node01 --ignore-daemonsets --force
node/node01 already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-flannel-ds-4wk5d, kube-system/kube-proxy-m86qr; deleting Pods not managed by ReplicationController, ReplicaSet, Job, DaemonSet or StatefulSet: default/hr-app
evicting pod default/hr-app
pod/hr-app evicted
node/node01 drained
```

4. Oops! We did not want to do that! hr-app is a critical application that should not be destroyed. We have now reverted back to the previous state and re-deployed hr-app as a deployment.

hr-app is a critical app and we do not want it to be removed and we do not want to schedule any more pods on node01.
Mark node01 as unschedulable so that no new pods are scheduled on this node.

Make sure that hr-app is not affected.

```bash
# k cordon node01
node/node01 cordoned
```

### Cluster Upgrade

1. What is the current version of the cluster?
```bash
# kubectl get nodes
NAME           STATUS   ROLES    AGE   VERSION
controlplane   Ready    master   84m   v1.19.0
node01         Ready    <none>   83m   v1.19.0
```

2. What is the latest stable version available for upgrade?
```bash
# kubeadm upgrade plan  
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: v1.19.0
[upgrade/versions] kubeadm version: v1.19.0
I0827 02:41:46.916624   46396 version.go:252] remote version is much newer: v1.25.0; falling back to: stable-1.19
[upgrade/versions] Latest stable version: v1.19.16
[upgrade/versions] Latest stable version: v1.19.16
[upgrade/versions] Latest version in the v1.19 series: v1.19.16
[upgrade/versions] Latest version in the v1.19 series: v1.19.16

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   CURRENT       AVAILABLE
kubelet     2 x v1.19.0   v1.19.16

Upgrade to the latest version in the v1.19 series:

COMPONENT                 CURRENT   AVAILABLE
kube-apiserver            v1.19.0   v1.19.16
kube-controller-manager   v1.19.0   v1.19.16
kube-scheduler            v1.19.0   v1.19.16
kube-proxy                v1.19.0   v1.19.16
CoreDNS                   1.7.0     1.7.0
etcd                      3.4.9-1   3.4.9-1

```

3. We will be upgrading the master node first. Drain the master node of workloads and mark it UnSchedulable

```bash
# kubectl drain controlplane --ignore-daemonsets
```

4. Upgrade the `controlplane` components to exact version `v1.20.0`

[Upgrading kubeadm clusters](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)

Upgrade kubeadm tool (if not already), then the master components, and finally the kubelet. Practice referring to the kubernetes documentation page. Note: While upgrading kubelet, if you hit dependency issue while running the `apt-get upgrade kubelet` command, use the `apt install kubelet=1.20.0-00` command instead

On the controlplane node, run the command run the following commands:

`apt update`  
This will update the package lists from the software repository.

`apt install kubeadm=1.20.0-00`  
This will install the kubeadm version 1.20

`kubeadm upgrade apply v1.20.0`  
This will upgrade kubernetes controlplane. Note that this can take a few minutes.

`apt install kubelet=1.20.0-00`  
This will update the kubelet with the version 1.20.
You may need to restart kubelet after it has been upgraded.  
Run: `systemctl restart kubelet`  

5. Next is the worker node. Drain the worker node of the workloads and mark it UnSchedulable

```bash
# ssh node01

# apt update
# apt install kubeadm=1.20.0-00
# kubeadm upgrade node

# apt install kubelet=1.20.0-00

# systemctl restart kubelet
```

### Backup and Restore Methods

1. What is the version of ETCD running on the cluster?
```yaml
Containers:
  etcd:
    Container ID:  docker://35cd0d9d5499de2444aec6eb2ff10d94e50fc42e9f175352f2b640d150d7cb3d
    Image:         k8s.gcr.io/etcd:3.5.1-0
```

2. At what address can you reach the ETCD cluster from the controlplane node?
```yaml
c263
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --listen-client-urls=https://127.0.0.1:2379,https://10.10.177.9:2379
```

3. Where is the ETCD server certificate file located?
```yaml
    Command:
      etcd
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
```

```yaml
c263
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

4. The master node in our cluster is planned for a regular maintenance reboot tonight. While we do not anticipate anything to go wrong, we are required to take the necessary backups. Take a snapshot of the ETCD database using the built-in snapshot functionality.

Store the backup file at location `/opt/snapshot-pre-boot.db`

```bash
root@controlplane:~# ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/snapshot-pre-boot.db

Snapshot saved at /opt/snapshot-pre-boot.db
root@controlplane:~# 
```

5. Wake up! We have a conference call! After the reboot the master nodes came back online, but none of our applications are accessible. Check the status of the applications on the cluster. What's wrong?

6. Luckily we took a backup. Restore the original state of the cluster using the backup file.

First Restore the snapshot:
```bash
root@controlplane:~# ETCDCTL_API=3 etcdctl  --data-dir /var/lib/etcd-from-backup \
snapshot restore /opt/snapshot-pre-boot.db

2022-03-25 09:19:27.175043 I | mvcc: restore compact to 2552
2022-03-25 09:19:27.266709 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32
root@controlplane:~# 
```  

Next, update the `/etc/kubernetes/manifests/etcd.yaml`:  

We have now restored the etcd snapshot to a new path on the controlplane - /var/lib/etcd-from-backup, so, the only change to be made in the YAML file, is to change the hostPath for the volume called etcd-data from old directory (`/var/lib/etcd`) to the new directory (`/var/lib/etcd-from-backup`).

```yaml
  volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data
```

With this change, `/var/lib/etcd` on the container points to `/var/lib/etcd-from-backup` on the controlplane (which is what we want)

When this file is updated, the ETCD pod is automatically re-created as this is a static pod placed under the /etc/kubernetes/manifests directory.

> Note 1: As the ETCD pod has changed it will automatically restart, and also kube-controller-manager and kube-scheduler. Wait 1-2 to mins for this pods to restart. You can run a watch "docker ps | grep etcd" command to see when the ETCD pod is restarted.   

> Note 2: If the etcd pod is not getting Ready 1/1, then restart it by kubectl delete pod -n kube-system etcd-controlplane and wait 1 minute.  

> Note 3: This is the simplest way to make sure that ETCD uses the restored data after the ETCD pod is recreated. You don't have to change anything else.

## 7. Security
[Kubernetes Certificate Health Check Spreadsheet](https://github.com/mmumshad/kubernetes-the-hard-way/tree/master/tools)
### View Certificates

1. Identify the certificate file used for the `kube-api server`  

```yaml
    Command:
      kube-apiserver
      --advertise-address=10.22.252.9
      --allow-privileged=true
      --authorization-mode=Node,RBAC
      --client-ca-file=/etc/kubernetes/pki/ca.crt
      --enable-admission-plugins=NodeRestriction
      --enable-bootstrap-token-auth=true
      --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
      --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
      --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
      --etcd-servers=https://127.0.0.1:2379
      --insecure-port=0
      --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
      --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
      --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
      --requestheader-allowed-names=front-proxy-client
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
      --requestheader-extra-headers-prefix=X-Remote-Extra-
      --requestheader-group-headers=X-Remote-Group
      --requestheader-username-headers=X-Remote-User
      --secure-port=6443
      --service-account-issuer=https://kubernetes.default.svc.cluster.local
      --service-account-key-file=/etc/kubernetes/pki/sa.pub
      --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
      --service-cluster-ip-range=10.96.0.0/12
      --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
      --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
```

2. Identify the Certificate file used to authenticate kube-apiserver as a client to `ETCD Server`
```yaml
    Command:
      kube-apiserver
      --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
```

3. Identify the key used to authenticate kubeapi-server to the kubelet server

```yaml
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
```

4. Identify the ETCD Server Certificate used to host ETCD server

```bash
# k describe po etcd-controlplane -n kube-system
...
    Command:
      etcd
      --advertise-client-urls=https://10.22.252.9:2379
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --initial-advertise-peer-urls=https://10.22.252.9:2380
      --initial-cluster=controlplane=https://10.22.252.9:2380
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://10.22.252.9:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://10.22.252.9:2380
      --name=controlplane
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --snapshot-count=10000
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
...
```

5. Identify the ETCD Server CA Root Certificate used to serve ETCD Server
```bash
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

6. What is the Common Name (CN) configured on the Kube API Server Certificate?  

OpenSSL Syntax: `openssl x509 -in file-path.crt -text -noout`

```bash
# openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        ...
        Issuer: CN = kubernetes
        ...
        Subject: CN = kube-apiserver
```

7. What is the name of the CA who issued the Kube API Server Certificate?

8. Which of the below alternate names is not configured on the Kube API Server Certificate?
```bash
            X509v3 Subject Alternative Name: 
                DNS:controlplane, DNS:kubernetes, DNS:kubernetes.default, DNS:kubernetes.default.svc, DNS:kubernetes.def
```

9. What is the Common Name (CN) configured on the ETCD Server certificate?

```bash
# openssl x509 -in /etc/kubernetes/pki/etcd/server.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 5601635745801875035 (0x4dbd020835a6ae5b)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = etcd-ca
        Validity
            Not Before: Aug 27 12:01:34 2022 GMT
            Not After : Aug 27 12:01:34 2023 GMT
        Subject: CN = control
        plane
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:e0:ad:26:8f:72:da:5d:c4:50:bd:0a:43:e6:a2:
                    35:42:34:e7:6d:25:7b:e2:42:20:0a:9d:35:6e:c6:
                    3e:36:f0:a9:3d:aa:65:6a:c1:f3:e8:b4:ec:32:57:
                    68:c8:00:66:e9:c4:c7:cb:22:72:0f:57:84:ee:76:
                    3b:9d:21:53:ea:2a:aa:42:f1:fe:93:1b:66:fd:5e:
                    30:09:2e:97:fc:2f:f7:da:d3:41:04:19:6d:f4:fe:
                    40:55:8e:63:17:ad:9d:a5:a3:a8:1b:46:05:b0:9a:
                    c1:e2:71:da:04:fa:7f:55:8a:48:e2:0f:e3:ab:10:
                    99:ae:e1:ed:72:cb:aa:55:bd:b7:32:a8:4b:d4:f7:
                    fa:bb:4d:c0:0a:10:1c:67:85:a6:90:71:7a:2e:d3:
                    0e:05:0f:8f:17:33:9f:7f:45:f4:db:06:97:50:8a:
                    01:9c:3a:5f:71:1c:b9:84:69:7b:5c:67:e0:36:1f:
                    ab:43:5f:9b:2a:dd:4f:ec:df:1a:c6:11:39:14:1b:
                    9a:90:6c:a6:e7:2a:dd:4b:8c:af:9c:d9:9d:a3:ba:
                    24:c7:0c:43:c4:24:ee:ca:f0:5a:cf:ac:72:f5:60:
                    25:58:63:8f:f0:c4:61:a8:1a:98:43:02:37:d0:ea:
                    a5:59:e0:65:51:b0:24:04:11:25:06:62:cc:93:b1:
                    be:63
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Authority Key Identifier: 
                keyid:9C:22:57:35:7A:ED:C2:E2:65:57:05:1D:D2:44:36:6E:5E:F9:F6:E1

            X509v3 Subject Alternative Name: 
                DNS:controlplane, DNS:localhost, IP Address:10.22.252.9, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1
    Signature Algorithm: sha256WithRSAEncryption
         2a:c0:f2:cd:0e:30:3a:35:02:c2:ab:a2:6d:76:11:64:81:53:
         f6:e0:e6:8a:5f:59:92:88:e4:d8:ad:90:36:05:16:bb:cc:2c:
         f0:6d:76:dd:58:cd:de:51:a2:0a:83:12:19:54:e9:5c:3c:33:
         99:1c:e3:67:b4:57:da:0d:4f:9a:d8:78:e2:6e:57:c5:06:cc:
         27:31:57:a2:a9:51:99:31:f3:01:bf:41:92:54:85:3e:f7:4e:
         ea:ce:65:e5:cc:84:3c:79:90:1e:80:42:cd:84:99:ea:15:51:
         8d:05:12:dd:0f:d3:2c:89:84:5e:45:ea:fc:ef:4a:ce:0c:4f:
         ad:05:a1:33:67:93:7a:c7:9a:d0:ef:75:f8:a1:0d:5b:42:3f:
         9f:4d:c6:f2:07:bb:da:0c:a4:3e:83:15:d3:71:a3:66:c6:de:
         71:ae:12:c6:eb:bb:5c:e9:84:6d:b8:44:cd:52:b5:fb:63:95:
         2d:e3:0f:d1:45:34:99:2f:cb:64:d8:c7:5a:75:ec:ba:50:7a:
         b9:f7:f9:0d:05:08:43:77:5c:47:2f:2b:ae:dd:4d:fb:05:73:
         ce:25:f4:19:25:49:ca:56:68:04:58:41:4d:be:6f:2e:b9:f9:
         af:a7:1c:4e:f7:7b:e5:6c:a4:fe:46:99:e1:6f:5c:e6:09:44:
         e1:38:77:65
```

10. How long, from the issued date, is the Kube-API Server Certificate valid for?  
File: `/etc/kubernetes/pki/apiserver.crt`

```bash
# openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
Certificate:
    Data:
        ...
        Validity
            Not Before: Aug 27 12:01:33 2022 GMT
            Not After : Aug 27 12:01:33 2023 GMT
```

11. How long, from the issued date, is the Root CA Certificate valid for?  
File: `/etc/kubernetes/pki/ca.crt`

```bash
# openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 0 (0x0)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Aug 27 12:01:33 2022 GMT
            Not After : Aug 24 12:01:33 2032 GMT
```

12. Kubectl suddenly stops responding to your commands. Check it out! Someone recently modified the `/etc/kubernetes/manifests/etcd.yaml` file. You are asked to investigate and fix the issue. Once you fix the issue wait for sometime for kubectl to respond. Check the logs of the ETCD container.

The certificate file used here is incorrect.  
It is set to `/etc/kubernetes/pki/etcd/server-certificate.crt` which does not exist.  
As we saw in the previous questions the correct path should be `/etc/kubernetes/pki/etcd/server.crt`.

```bash
root@controlplane:~# ls -l /etc/kubernetes/pki/etcd/server* | grep .crt
-rw-r--r-- 1 root root 1188 May 20 00:41 /etc/kubernetes/pki/etcd/server.crt
root@controlplane:~# 
```

Update the YAML file with the correct certificate path and wait for the ETCD pod to be recreated. wait for the kube-apiserver to get to a Ready state.

NOTE: It may take a few minutes for the kubectl commands to work again so please be patient.

```bash
# vi /etc/kubernetes/manifests/etcd.yaml
apiVersion: v1
kind: Pod
metadata:
  ...
spec:
  containers:
  - command:
    - etcd
    - --advertise-client-urls=https://10.22.252.9:2379
    - --cert-file=/etc/kubernetes/pki/etcd/server.crt
    - --client-cert-auth=true
    - --data-dir=/var/lib/etcd
    - --initial-advertise-peer-urls=https://10.22.252.9:2380
    - --initial-cluster=controlplane=https://10.22.252.9:2380
    - --key-file=/etc/kubernetes/pki/etcd/server.key
    - --listen-client-urls=https://127.0.0.1:2379,https://10.22.252.9:2379
    - --listen-metrics-urls=http://127.0.0.1:2381
    - --listen-peer-urls=https://10.22.252.9:2380
    - --name=controlplane
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-client-cert-auth=true
    - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --snapshot-count=10000
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

13. The kube-api server stopped again! Check it out. Inspect the kube-api server logs and identify the root cause and fix the issue.  

Run `docker ps -a` command to identify the kube-api server container. Run `docker logs container-id` command to view the logs.

```bash
# docker ps -a | grep kube-api
0d077f38c8ae        ca9843d3b545           "kube-apiserver --ad…"   36 seconds ago       Exited (1) 14 seconds ago                             k8s_kube-apiserver_kube-apiserver-controlplane_kube-system_3dc139844efbc9777eae13bd27ae32f8_2
2f2097b20255        k8s.gcr.io/pause:3.2   "/pause"                 About a minute ago   Up About a minute                                     k8s_POD_kube-apiserver-controlplane_kube-system_3dc139844efbc9777eae13bd27ae32f8_0

# docker logs 0d077f38c8ae
Flag --insecure-port has been deprecated, This flag has no effect now and will be removed in v1.24.
I0827 13:28:59.838674       1 server.go:632] external host was not specified, using 10.22.252.9
I0827 13:28:59.839314       1 server.go:182] Version: v1.20.0
I0827 13:29:00.331430       1 shared_informer.go:240] Waiting for caches to sync for node_authorizer
I0827 13:29:00.332583       1 plugins.go:158] Loaded 12 mutating admission controller(s) successfully in the following order: NamespaceLifecycle,LimitRanger,ServiceAccount,NodeRestriction,TaintNodesByCondition,Priority,DefaultTolerationSeconds,DefaultStorageClass,StorageObjectInUseProtection,RuntimeClass,DefaultIngressClass,MutatingAdmissionWebhook.
I0827 13:29:00.332612       1 plugins.go:161] Loaded 10 validating admission controller(s) successfully in the following order: LimitRanger,ServiceAccount,Priority,PersistentVolumeClaimResize,RuntimeClass,CertificateApproval,CertificateSigning,CertificateSubjectRestriction,ValidatingAdmissionWebhook,ResourceQuota.
I0827 13:29:00.333831       1 plugins.go:158] Loaded 12 mutating admission controller(s) successfully in the following order: NamespaceLifecycle,LimitRanger,ServiceAccount,NodeRestriction,TaintNodesByCondition,Priority,DefaultTolerationSeconds,DefaultStorageClass,StorageObjectInUseProtection,RuntimeClass,DefaultIngressClass,MutatingAdmissionWebhook.
I0827 13:29:00.333857       1 plugins.go:161] Loaded 10 validating admission controller(s) successfully in the following order: LimitRanger,ServiceAccount,Priority,PersistentVolumeClaimResize,RuntimeClass,CertificateApproval,CertificateSigning,CertificateSubjectRestriction,ValidatingAdmissionWebhook,ResourceQuota.
I0827 13:29:00.336611       1 client.go:360] parsed scheme: "endpoint"
I0827 13:29:00.336674       1 endpoint.go:68] ccResolverWrapper: sending new addresses to cc: [{https://127.0.0.1:2379  <nil> 0 <nil>}]
W0827 13:29:00.340488       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
I0827 13:29:01.331260       1 client.go:360] parsed scheme: "endpoint"
I0827 13:29:01.331319       1 endpoint.go:68] ccResolverWrapper: sending new addresses to cc: [{https://127.0.0.1:2379  <nil> 0 <nil>}]
W0827 13:29:01.335016       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:01.344024       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:02.339112       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:02.824507       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:03.739161       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:05.405697       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:06.141684       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:09.977855       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:10.714708       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:17.281320       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
W0827 13:29:17.343731       1 clientconn.go:1223] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379  <nil> 0 <nil>}. Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
Error: context deadline exceeded
```

This indicates an issue with the `ETCD CA certificate` used by the kube-apiserver. Correct it to use the file `/etc/kubernetes/pki/etcd/ca.crt`.

Once the YAML file has been saved, wait for the kube-apiserver pod to be Ready. This can take a couple of minutes.

```bash
# vi /etc/kubernetes/manifests/kube-apiserver.yaml 
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
    - --etcd-servers=https://127.0.0.1:2379

```

### Certificates API
1. A new member akshay joined our team. He requires access to our cluster. The Certificate Signing Request is at the /root location.


```bash
# cat akshay.csr 
-----BEGIN CERTIFICATE REQUEST-----
MIICVjCCAT4CAQAwETEPMA0GA1UEAwwGYWtzaGF5MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAtY5AR2S90s28u3Pe773ll2pq/5xoRFVsjJoIXrgnbsF2
+ZiwExIQw7Akm/VRl07gz8LVpZTvPNYujhf44Sy14zN0eZ3Gwb3nHOZsaiiOESVl
4dpaOpp2SJynhUB2ss+3pUTVklBPX/Ovhm7rPLlN8b3XVeuHJ4ovPg6Cvj129D57
LxsJwLMLmbad9XfnCee3k5nJ1Qcp0e3/+gL8D0tltbVx6rRPtucbrpCVV/L1x1/T
Vedxnkk/vBFjKASdbZeNHfp0t5Kay+/ylGl6H27Rbmwb4r8fQg2+GgdotiNid6EE
ig6e0olo3OYl7bJcGQag1Q7rSZwp4ULNN2eLH/XTeQIDAQABoAAwDQYJKoZIhvcN
AQELBQADggEBAHJga8b2aU9OhtD9g9HLGkF+Cu7wCmoZeN7ksy9+8NzrfWORfhUt
HiJJwzPs8i9l4we/v5bE4dvh/XMAlApSFiMwT+5TFY6mn4Sbs+tPxbR6WNEwLtMq
xFKGyNcp3QMnaViiufR5MzAd7zoy+zogucbN77a2xKY+Rs97W16Ft5Q5AR/mamf8
+fm+8QVt9DCKbYRss/hDnhSk9S+vbWF+9EsSwBAStBc/08vH870hqv1OLvrwC3/N
phku0fClfgQmrmEpBk5vlWIv1bUEwhnrjAkYKAhjhTzuwgYYY8bhw9BdlJUC2jdz
Xl4YvxaIUG/BGeVsX2AafByw0i95uQ+mjZ8=
-----END CERTIFICATE REQUEST-----
```

2. Create a CertificateSigningRequest object with the name akshay with the contents of the akshay.csr file

As of kubernetes 1.19, the API to use for CSR is `certificates.k8s.io/v1`.  

Please note that an additional field called `signerName` should also be added when creating CSR. For client authentication to the API server we will use the built-in signer `kubernetes.io/kube-apiserver-client`.

[Create CertificateSigningRequest](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/#create-certificatesigningrequest)

```bash
# cat akshay.csr | base64 -w 0
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQXRZNUFSMlM5MHMyOHUzUGU3NzNsbDJwcS81eG9SRlZzakpvSVhyZ25ic0YyCitaaXdFeElRdzdBa20vVlJsMDdnejhMVnBaVHZQTll1amhmNDRTeTE0ek4wZVozR3diM25IT1pzYWlpT0VTVmwKNGRwYU9wcDJTSnluaFVCMnNzKzNwVVRWa2xCUFgvT3ZobTdyUExsTjhiM1hWZXVISjRvdlBnNkN2ajEyOUQ1NwpMeHNKd0xNTG1iYWQ5WGZuQ2VlM2s1bkoxUWNwMGUzLytnTDhEMHRsdGJWeDZyUlB0dWNicnBDVlYvTDF4MS9UClZlZHhua2svdkJGaktBU2RiWmVOSGZwMHQ1S2F5Ky95bEdsNkgyN1JibXdiNHI4ZlFnMitHZ2RvdGlOaWQ2RUUKaWc2ZTBvbG8zT1lsN2JKY0dRYWcxUTdyU1p3cDRVTE5OMmVMSC9YVGVRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBSEpnYThiMmFVOU9odEQ5ZzlITEdrRitDdTd3Q21vWmVON2tzeTkrOE56cmZXT1JmaFV0CkhpSkp3elBzOGk5bDR3ZS92NWJFNGR2aC9YTUFsQXBTRmlNd1QrNVRGWTZtbjRTYnMrdFB4YlI2V05Fd0x0TXEKeEZLR3lOY3AzUU1uYVZpaXVmUjVNekFkN3pveSt6b2d1Y2JONzdhMnhLWStSczk3VzE2RnQ1UTVBUi9tYW1mOAorZm0rOFFWdDlEQ0tiWVJzcy9oRG5oU2s5Uyt2YldGKzlFc1N3QkFTdEJjLzA4dkg4NzBocXYxT0x2cndDMy9OCnBoa3UwZkNsZmdRbXJtRXBCazV2bFdJdjFiVUV3aG5yakFrWUtBaGpoVHp1d2dZWVk4Ymh3OUJkbEpVQzJqZHoKWGw0WXZ4YUlVRy9CR2VWc1gyQWFmQnl3MGk5NXVRK21qWjg9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
```

```bash
vi akshay-csr.yaml

---
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  groups:
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQXY4azZTTE9HVzcrV3JwUUhITnI2TGFROTJhVmQ1blNLajR6UEhsNUlJYVdlCmJ4RU9JYkNmRkhKKzlIOE1RaS9hbCswcEkwR2xpYnlmTXozL2lGSWF3eGVXNFA3bDJjK1g0L0lqOXZQVC9jU3UKMDAya2ZvV0xUUkpQbWtKaVVuQTRpSGxZNDdmYkpQZDhIRGFuWHM3bnFoenVvTnZLbWhwL2twZUVvaHd5MFRVMAo5bzdvcjJWb1hWZTVyUnNoMms4dzV2TlVPL3BBdEk4VkRydUhCYzRxaHM3MDI1ZTZTUXFDeHUyOHNhTDh1blJQCkR6V2ZsNVpLcTVpdlJNeFQrcUo0UGpBL2pHV2d6QVliL1hDQXRrRVJyNlMwak9XaEw1Q0ErVU1BQmd5a1c5emQKTmlXbnJZUEdqVWh1WjZBeWJ1VzMxMjRqdlFvbndRRUprNEdoayt2SU53SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBQi94dDZ2d2EweWZHZFpKZ1k2ZDRUZEFtN2ZiTHRqUE15OHByTi9WZEdxN25oVDNUUE5zCjEwRFFaVGN6T21hTjVTZmpTaVAvaDRZQzQ0QjhFMll5Szg4Z2lDaUVEWDNlaDFYZnB3bnlJMVBDVE1mYys3cWUKMkJZTGJWSitRY040MDU4YituK24wMy9oVkN4L1VRRFhvc2w4Z2hOaHhGck9zRUtuVExiWHRsK29jQ0RtN3I3UwpUYTFkbWtFWCtWUnFJYXFGSDd1dDJveHgxcHdCdnJEeGUvV2cybXNqdHJZUXJ3eDJmQnErQ2Z1dm1sVS9rME4rCml3MEFjbVJsMy9veTdqR3ptMXdqdTJvNG4zSDNKQ25SbE41SnIyQkZTcFVQU3dCL1lUZ1ZobHVMNmwwRERxS3MKNTdYcEYxcjZWdmJmbTRldkhDNnJCSnNiZmI2ZU1KejZPMUU9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

```bash
# kubectl get csr
NAME        AGE   SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
akshay      21s   kubernetes.io/kube-apiserver-client           kubernetes-admin           <none>              Pending
```

3. Approve the CSR Request
```bash
# kubectl certificate approve akshay
certificatesigningrequest.certificates.k8s.io/akshay approved
```

4. During a routine check you realized that there is a new CSR request in place. What is the name of this request?

5. Hmmm.. You are not aware of a request coming in. What groups is this CSR requesting access to?  
Check the details about the request. Preferebly in YAML

```bash
# kubectl get csr agent-smith -o yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  creationTimestamp: "2022-08-28T23:47:25Z"
  name: agent-smith
  resourceVersion: "2617"
  uid: 187e7363-6c14-472e-80da-acb303e5353d
spec:
  groups:
  - system:masters
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1dEQ0NBVUFDQVFBd0V6RVJNQThHQTFVRUF3d0libVYzTFhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQgpBUVVBQTRJQkR3QXdnZ0VLQW9JQkFRRE8wV0pXK0RYc0FKU0lyanBObzV2UklCcGxuemcrNnhjOStVVndrS2kwCkxmQzI3dCsxZUVuT041TXVxOTlOZXZtTUVPbnJEVU8vdGh5VnFQMncyWE5JRFJYall5RjQwRmJtRCs1eld5Q0sKeTNCaWhoQjkzTUo3T3FsM1VUdlo4VEVMcXlhRGtuUmwvanYvU3hnWGtvazBBQlVUcFdNeDRCcFNpS2IwVSt0RQpJRjVueEF0dE1Wa0RQUTdOYmVaUkc0M2IrUVdsVkdSL3o2RFdPZkpuYmZlek90YUF5ZEdMVFpGQy93VHB6NTJrCkVjQ1hBd3FDaGpCTGt6MkJIUFI0Sjg5RDZYYjhrMzlwdTZqcHluZ1Y2dVAwdEliT3pwcU52MFkwcWRFWnB3bXcKajJxRUwraFpFV2trRno4MGxOTnR5VDVMeE1xRU5EQ25JZ3dDNEdaaVJHYnJBZ01CQUFHZ0FEQU5CZ2txaGtpRwo5dzBCQVFzRkFBT0NBUUVBUzlpUzZDMXV4VHVmNUJCWVNVN1FGUUhVemFsTnhBZFlzYU9SUlFOd0had0hxR2k0CmhPSzRhMnp5TnlpNDRPT2lqeWFENnRVVzhEU3hrcjhCTEs4S2czc3JSRXRKcWw1ckxaeTlMUlZyc0pnaEQ0Z1kKUDlOTCthRFJTeFJPVlNxQmFCMm5XZVlwTTVjSjVURjUzbGVzTlNOTUxRMisrUk1uakRRSjdqdVBFaWM4L2RoawpXcjJFVU02VWF3enlrcmRISW13VHYybWxNWTBSK0ROdFYxWWllKzBIOS9ZRWx0K0ZTR2poNUw1WVV2STFEcWl5CjRsM0UveTNxTDcxV2ZBY3VIM09zVnBVVW5RSVNNZFFzMHFXQ3NiRTU2Q0M1RGhQR1pJcFVibktVcEF3a2ErOEUKdndRMDdqRytocGtueG11RkFlWHhnVXdvZEFMYUo3anUvVERJY3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0K
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - digital signature
  - key encipherment
  - server auth
  username: agent-x
status: {}
```

6. That doesn't look very right. Reject that request.

```bash
# kubectl certificate deny agent-smith
certificatesigningrequest.certificates.k8s.io/agent-smith denied
```

### KubeConfig

1. Where is the default kubeconfig file located in the current environment?  
`/root/.kube`

2. How many clusters are defined in the default kubeconfig file?

```bash
# kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://controlplane:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
```

3. How many contexts are defined in the default kubeconfig file?
4. What is the user configured in the current context?
5. A new kubeconfig file named `my-kube-config` is created. It is placed in the `/root` directory. How many clusters are defined in that kubeconfig file?

6. How many contexts are configured in the `my-kube-config` file?  

```bash
# cat my-kube-config 
apiVersion: v1
kind: Config

clusters:
- name: production
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: development
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: kubernetes-on-aws
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: test-cluster-1
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

contexts:
- name: test-user@development
  context:
    cluster: development
    user: test-user

- name: aws-user@kubernetes-on-aws
  context:
    cluster: kubernetes-on-aws
    user: aws-user

- name: test-user@production
  context:
    cluster: production
    user: test-user

- name: research
  context:
    cluster: test-cluster-1
    user: dev-user

users:
- name: test-user
  user:
    client-certificate: /etc/kubernetes/pki/users/test-user/test-user.crt
    client-key: /etc/kubernetes/pki/users/test-user/test-user.key
- name: dev-user
  user:
    client-certificate: /etc/kubernetes/pki/users/dev-user/developer-user.crt
    client-key: /etc/kubernetes/pki/users/dev-user/dev-user.key
- name: aws-user
  user:
    client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
    client-key: /etc/kubernetes/pki/users/aws-user/aws-user.key
```

7. I would like to use the `dev-user` to access `test-cluster-1`. Set the current context to the right one so I can do that.  
Once the right context is identified, use the `kubectl config use-context` command.  


To use that context, run the command: `kubectl config --kubeconfig=/root/my-kube-config use-context research`
To know the current context, run the command: `kubectl config --kubeconfig=/root/my-kube-config current-context`

8. We don't want to have to specify the kubeconfig file option on each command. Make the my-kube-config file the default kubeconfig.

### Role Based Access Control

1. Inspect the environment and identify the authorization modes configured on the cluster.
```bash
spec:
  containers:
  - command:
    - kube-apiserver
    ...
    - --authorization-mode=Node,RBAC
```

2. How many roles exist in all namespace?

```bash
# kubectl get roles -A
NAMESPACE     NAME                                             CREATED AT
blue          developer                                        2022-08-29T11:18:23Z
kube-public   kubeadm:bootstrap-signer-clusterinfo             2022-08-29T11:15:52Z
kube-public   system:controller:bootstrap-signer               2022-08-29T11:15:50Z
kube-system   extension-apiserver-authentication-reader        2022-08-29T11:15:50Z
kube-system   kube-proxy                                       2022-08-29T11:15:52Z
kube-system   kubeadm:kubelet-config-1.23                      2022-08-29T11:15:51Z
kube-system   kubeadm:nodes-kubeadm-config                     2022-08-29T11:15:51Z
kube-system   system::leader-locking-kube-controller-manager   2022-08-29T11:15:50Z
kube-system   system::leader-locking-kube-scheduler            2022-08-29T11:15:50Z
kube-system   system:controller:bootstrap-signer               2022-08-29T11:15:50Z
kube-system   system:controller:cloud-provider                 2022-08-29T11:15:50Z
kube-system   system:controller:token-cleaner                  2022-08-29T11:15:50Z
```

3. What are the resources the `kube-proxy` role in the `kube-system` namespace is given access to?
```bash
# kubectl describe role kube-proxy -n kube-system
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 [kube-proxy]    [get]
```

4. Which account is the kube-proxy role assigned to?
```bash
# kubectl describe rolebinding kube-proxy -n kube-system
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  kube-proxy
Subjects:
  Kind   Name                                             Namespace
  ----   ----                                             ---------
  Group  system:bootstrappers:kubeadm:default-node-token  
```

5. A user `dev-user` is created. User's details have been added to the `kubeconfig` file. Inspect the permissions granted to the user. Check if the user can list pods in the `default` namespace.


Use the `--as dev-user` option with `kubectl` to run commands as the `dev-user`.

```bash
#  kubectl get pods --as dev-user
Error from server (Forbidden): pods is forbidden: User "dev-user" cannot list resource "pods" in API group "" in the namespace "default"
```

6. Create the necessary roles and role bindings required for the `dev-user` to create, list and delete pods in the default namespace.
- Role: developer
- Role Resources: pods
- Role Actions: list
- Role Actions: create
- Role Actions: delete
- RoleBinding: dev-user-binding
- RoleBinding: Bound to dev-user

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "create","delete"]

---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

7. A set of new roles and role-bindings are created in the `blue` namespace for the `dev-user`. However, the `dev-user` is unable to get details of the `dark-blue-app` pod in the `blue` namespace. Investigate and fix the issue.

We have created the required roles and rolebindings, but something seems to be wrong.

```bash
# kubectl edit role developer -n blue
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  creationTimestamp: "2022-08-29T11:18:23Z"
  name: developer
  namespace: blue
  resourceVersion: "703"
  uid: bdf2f357-9b78-418d-959d-e934c9be1dd6
rules:
- apiGroups:
  - ""
  resourceNames:
  - blue-app
  resources:
  - pods
  verbs:
  - get
  - watch
  - create
  - delete


# kubectl get pod dark-blue-app  -n blue --as dev-user
NAME            READY   STATUS    RESTARTS   AGE
dark-blue-app   1/1     Running   0          32m  
```







