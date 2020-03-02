 Labels are used for classifying resources on the cluster. Labels do not need to be unique and can be multiple on each resource. Labels are used as a way to segregate resource and filter deployments

---
#### Scenario: You have nodes which are hardened for DEV and others hardened for QA. You want to deploy a container defination on DEV. You want to avoid the container to go into QA nodes even if someone tries to.

This situation can be solved by labels. 
- First tag our nodes with the right environment tags. 
- Add **nodeSelector** in pod configuration to select the type of node you want to deploy to.

##### Tag the nodes
```
kubectl label nodes node1 environment=dev
kubectl label nodes node2 environment=qa
```
##### Add nodeSelector to pod defination
```
apiVersion: v1
kind: Pod
metadata:
  name: helloworld-pod
  labels:
    app: helloworld
spec:
  containers:
  -   name: k8-demo
      image: wardviaene/k8s-demo
      ports:
          - containerPort: 80
  nodeSelector: 
      environment: dev
      
```
---
### Demo
- Lets say your node are not labeled dev,qa ,etc and your pod defination has **nodeSelector: dev** , this will cause a failure in deployment. The pods will be in a state of **pending** till you label any node as dev,qa, etc. 
- label your pods
```
kubectl label nodes <NODE-NAME> environemnt=dev
```
- the pods will soon after go into running from pending
```
$ kubectl get pods
```
  
