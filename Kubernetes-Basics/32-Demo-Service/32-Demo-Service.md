In this demo we will deploy a pod and a service to reach to that pod. As we are going to define the service and not just run **kubectl expose** , we will have defination for both

#### Deployment : helloworld-deployment.yaml
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
        - containerPort: 3000
          name: nodejs-port
```
**container-port** : Its the internal port open on the container.

- create the deployemnt
```
kubectl create -f helloworld-deployment.yaml
```
---
#### Service: helloworld-deployment-service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: helloworld-deployment-service
spec:
  selector:
    app: helloworld
  ports:
    - protocol: TCP
      port: 30001
      nodePort: 3000
      targetPort: nodejs-port
  type: NodePort
```
**"type: NodePort"** :   
**"targetPort: nodejs-port"** : 

- deploy the service after creating the deployment
```
$ kubectl create -f helloworld-deployment-service.yaml
```
This create a service to link to the pod deployments.

- reach the deployment
```
kubectl service helloworld-deployment-service --url
```
This gets you an external IP with the port 30001 for us to reach to the deployments on port 3000 internally.

- We can define the service
```
kubectl describe service helloworld-deployment-service
```
this gets us the internal IP address that the service gets within the cluster. That IP address can be used by other pods running inside the cluster to reach to our pods.  

