### In this lecture we deploy a container using k8 deployment object. Then we rollout a new version of the container and rollback the new version to the old version.

- We have a deployment yaml: helloworld.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: helloworld-deployment
  labels:
    app: helloworld
spec:
  replicas: 3
  selector:
    matchLabels:
      app: helloworld
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: k8s-demo
        image: wardviaene/k8s-demo
        ports:
        - containerPort: 80
          name: nodejs-port
```
- Create deployment on my local k8
  
```
$ kubectl create -f hellowrold.yaml 

deployment "hellowrold-deployment" created
```

- Get deployment status of the above deployment. This will show you how many replica sets have been created
```
$ kubectl get deplyoments
```
- Get the replica-set of the deployment  
**deployment objects use replica-sets and NOT replication-controller**
```
$ kubectl get rs
```
- Get the pods running the deployments
```
$ kubectl get pods
$ kubectl get pods --show-labels

```
- Expose the deployment service
```
$ kubectl expose deployment helloworld-deployment --type=NodePort
```
- Query the service exposed by the port.
```
$ kubectl get service   
```
this exposes the IP address of the service and the port

- Describe the service to get the detail of each container
```
kubectl describe service hellowrold-deployment
```
This gives the IP address of each of the 3 containers and the port. It also shows the port on the host machine that is mapped to this deployment.
- Get the URL of the deployment
```
$ minikube service helloworld-deployment --url
```
This gets you the actual IP address that you can connect to. The IP address that the other commands show are the internal IP address of the K8 cluster. 


####  we want to upgrade to a new container version.
- Find the status of the initial rollout
```
$ kubectl rollout status helloworld-deployment
```
- Change the container image in the deployemnt
```
kubectl set image helloworld-deployment k8s-demo=wardviaene/k8s-demo:2
```
Here "k8s-demo" was the initial image. The version was not specified so it takes the default "latest" version. After we published a version "2", this command will fetch the version "2" of the image from dockerhub.

- Get status of the rollout
```
$ kubectl rollout status hellowrold-deployment
```
- Get the detailed view of which pods are termianted and which pods are spinning up.
```
$ kubectl get pods
```
This will show us that 3 pods are terminating  and 3 new pods are Running for the particular deployment

- We can see the rollout history of the deployment
```
$ kubectl rollout history helloworld-deployment
REVISION        CHANGE-CAUSE
1               <none>
2               <none>

```    
This command shows you all the rollouts on the deployments and their "CHANGE-CAUSE" if we had provided any. Change cause is like a comment on the deployment

#### We want to rollback

- If we want to rollback to the previous version 
```
$ kubectl rollout undo helloworld-deployment
$ kubectl rollout status hellowrld-deployment
$ kubectl get pods
```
The last command will show us that 3 NEW pods are spinned up. 

- By default, k8 deployment objects keep only 2 version of the rollouts. To go back to older rollouts, we need to force a bigger history. this can be done on the deployment yaml by specifying **revisionHistoryLimit**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: helloworld-deployment
  labels:
    app: helloworld
spec:
  replicas: 3
  revisionHistoryLimit : 100
  selector:
    matchLabels:
      app: helloworld
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: k8s-demo
        image: wardviaene/k8s-demo
        ports:
        - containerPort: 80
          name: nodejs-port
```
This change will cause the rollout history to be longer
```
$ kubectl rollout history hellowrold-deployment
REVISION        CHANGE-CAUSE
1               <none>
2               <none>
3               <none>
4               <none>
5               <none>
6               <none>
```
- Rollout to a particular revision version
```
$ kubectl rollout undo helloworld-deployment --to-revision=3
```


-----
## Self-Evaluation:
-  why are deployment objects better than replication-sets or replication-controller
-  
