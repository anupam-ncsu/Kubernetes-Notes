**Kubernetes Services** are abstract way to expose an application running on a set of Pods as a network service. 
**Pods are very dynamic** , they come and go on the Kubernetes cluster.  
- When using a **Replication Controller** pods are terminated and created during scaling operations.    
- When using **deployments**,and updating the image version for instance, pods are terminated and new pods take the place of older pods.

That's why pods should never be accessed directly, but always through a **service.**  

 A service is the **logical bridge** between the mortal "pods" and other services or end-users.  It is an abstraction which defines a logical set of Pods and a policy by which to access them (sometimes this pattern is called a micro-service). 
 Example: When we used **kubectl expose** command earlier to get an IP address and port to reach our containers, we created a **service** for our pods.  

With Kubernetes you don’t need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives Pods their own IP addresses and a single DNS name for a set of Pods, and can load-balance across them.

 Creating a **service** creates a endpoint for our pods.  

 - **ClusterIP** : a virtual IP only reachable from within the cluster.
This is the default.

- **NodePort** : a port that is the same on each node that is also reachable externally. If you want to reach a pod externally this is what you need.

- **LoadBalancer** : this is used for production applications on the cloud. A LoadBalancer is created by the cloud provider that will route external traffic to every node on the nodePort. (ELB on AWS)
  
These options allows you to create a virtual IP or ports for you to reach you pods. We can also get **DNS names using ExternalNames**, which will be discussed later.  

A Service in Kubernetes is a REST(Representational State Transfer) object, similar to a Pod. Like all of the REST objects, you can POST a Service definition to the API server to create a new instance for that service.

For example, suppose we have a deployment that each listen on TCP port 3000 and carry a label **app: helloworld-deployment** . When we want to reach out to this deployment and run **kubectl expose**, we essentially create a service defination similiar to this :
```
apiVersion: v1
kind: Service
metadata:
  name: helloworld-deployment-service
spec:
  selector:
    app: helloworld-deployment
  ports:
    - protocol: TCP
      port: 30001
      targetPort: 3000
```
Here the port 30001 is choosen at random (default range for service is (30000-32767).





### External Refernces:
- https://kubernetes.io/docs/concepts/services-networking/service/#defining-a-service
- 