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

---
```
apiVersion: 
kind:
metadata:


spec:
```