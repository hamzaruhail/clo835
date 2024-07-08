
# Deploy a Python Application on Kubernetes

## Project Assets

| GitHub Repository | https://github.com/hamzaruhail/clo835/tree/master/project-2 |
| --- | --- |
| **DockerHub Repository** | **https://hub.docker.com/repository/docker/hamzaruhail/clo835-project-2/general** |

### **Project Overview**

In this project, we will deploy a Python web application that displays the current time in Toronto, Canada, using Kubernetes. 

### Instructions

**Fork and Clone the Repository with Python application** 
https://github.com/hamzaruhail/clo835/tree/master/project-2

Let’s now create a `Dockerfile` in the working directory as same as [`app.py`](http://app.py) file to containerize the existing application

```docker
# Get the latest python image
FROM python:latest
# Set working directory as /app
WORKDIR /app
# Copy the application file
COPY app.py .
# Run the python application
CMD ["python", "app.py"]
```

**Build and Push Docker Image**

```bash
docker login # provide proper credentials for DockerHub
docker build -t hamzaruhail/clo835-project-2:latest .
docker push hamzaruhail/clo835-project-2:latest .
```

###Getting started with Kubernetes, 

minikube start
```

Now, we can interact with Kubernetes with `kubectl` CLI.

Let’s create our deployment file first called `deployment.yaml` with following contents:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: toronto-app
  labels:
    app: toronto-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: toronto-app
  template:
    metadata:
      labels:
        app: toronto-app
    spec:
      containers:
      - name: toronto-app
        image: hamzaruhail/clo835-project-2:latest
        ports:
        - containerPort: 3030
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
          requests:
            memory: "64Mi"
            cpu: "250m"
```





Now these containers from NodePort has to expose the port 3030.

Create a `service.yaml` file with contents as such:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: toronto-app-service
  labels:
    app: toronto-app
spec:
  type: NodePort
  selector:
    app: toronto-app
  ports:
    - protocol: TCP
      port: 3030
      targetPort: 3030
      nodePort: 30000
```

**Deploy to Kubernetes and the NodePort service**

```bash
kubectl create -f deployment.yaml
kubectl create -f service.yaml
```

To validate if the resources has been appropriately reflected, run

```bash
kubectl get deployments # should see a new deployment created
# then, for the service
kubectl get svc # should see a NodePort service
```

**Testing the Application**


```bash
kubectl get nodes -o wide
```

Note the IP of the node.

To see if our application is serving properly, make an HTTP GET request to the node IP followed by the NodePort instructed in the `service.yaml` file.

```bash
curl <node-ip-from-minikube>:30000
```

You should see results now with showing real time Toronto Time.



---

**END!**
