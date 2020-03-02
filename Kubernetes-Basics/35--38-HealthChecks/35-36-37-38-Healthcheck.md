Pods and containers are wrapper around the app. K8 takes care of keeping them alive. but that doesnt mean that the app is functioning.

### Liveness Probe:
Liveness probe is how k8 monitor the health of the application.

#### deployment.yaml
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
        livenessProbe:
            httpGet:
                path: /
                port: nodejs-port
            initialDelaySeconds: 15
            periodSeconds: 30
            successThreshold: 1
            failureThreshold: 3
            periodSeconds: 10
```
The livenessProbe is the healthcheck setting in the k8 . the setting in the yaml file is checking for http 200 on the path. It will start after 15 seconds of pod launch and query every 10 seconds. Till it receives 1 success or 3 failure messages.

If K8 finds a pod unhealthy by this standard, it will kill the pod and launch a new pod.

### Readiness Probe: 
**Liveness probe** checks if a container is running. If a container fails liveness probe, container is destoyed and new container takes its place.  
**Readiness Probe** checks if a container is ready to serve traffic. If a container fails readiness, its IP address will be removed from the service directory and it will not serve anymore requests.  
The readiness probe makes sure that at start-up your container only serves traffic when its ready.
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
        livenessProbe:
            httpGet:
                path: /
                port: nodejs-port
            initialDelaySeconds: 15
            periodSeconds: 30
            successThreshold: 1
            failureThreshold: 3
            periodSeconds: 10
        readinessProbe:
            httpGet:
                path: /app
                port: nodejs-port
            initialDelaySeconds: 5
            periodSeconds: 10
```

Assuming you are running a web-app on a nginx container.
The difference betweeen running the first and the second yaml is:
- running the first yaml, the container will be immiditly ready to serve traffic once the liveness probe gets http 200 on the nginx default path. But nginx being ready doesnt mean you application is fully installed and ready to serve traffic.
- running the second yaml will first pass the livenessprobe and show the container as running. But the container will not show as ready till your application is installed and ready to serve traffic on path : **/app**
- If in the second case the liveness probe pass but the readiness probe fails. the container will not get registered to the service and no traffic will be sent. It will be in the same state till it passes the readiness probe. In this case, assuming the application installation takes time.



