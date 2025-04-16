# Building a Kubernetes Cluster with Minikube

This guide walks through setting up a local Kubernetes cluster using Minikube and deploying an NGINX application to demonstrate basic Kubernetes concepts.

## Prerequisites

- Windows, macOS, or Linux operating system
- Docker, VirtualBox, or Hyper-V for virtualization
- Admin privileges to install software

## Step 1: Install Minikube and Dependencies

### For Linux:
```bash
# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### For macOS:
```bash
# Install kubectl
brew install kubectl

# Install Minikube
brew install minikube
```

### For Windows:
```powershell
# Install kubectl
curl -LO "https://dl.k8s.io/release/v1.27.1/bin/windows/amd64/kubectl.exe"

# Install Minikube (via chocolatey)
choco install minikube
```

## Step 2: Start the Minikube Cluster

```bash
minikube start
```

Verify the cluster is running:
```bash
kubectl cluster-info
minikube status
```

## Step 3: Create Deployment YAML

Create a file named `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

## Step 4: Create Service YAML

Create a file named `service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: NodePort
```

## Step 5: Deploy the Application

```bash
# Apply the deployment
kubectl apply -f deployment.yaml

# Apply the service
kubectl apply -f service.yaml
```

## Step 6: Verify Deployment

```bash
# Check pods
kubectl get pods

# Check services
kubectl get services

# Check deployments
kubectl get deployments
```

## Step 7: Access the Application

```bash
# Get the URL for the service
minikube service nginx-service --url

# Open the application in your browser
minikube service nginx-service
```

## Step 8: Scale the Deployment

```bash
# Scale to 4 replicas
kubectl scale deployment nginx-deployment --replicas=4

# Verify the scaling
kubectl get pods
```

## Step 9: Check Logs and Details

```bash
# Get details of one pod (replace with your actual pod name)
kubectl describe pod $(kubectl get pods -l app=nginx -o jsonpath="{.items[0].metadata.name}")

# Get logs from a pod
kubectl logs $(kubectl get pods -l app=nginx -o jsonpath="{.items[0].metadata.name}")
```

## Step 10: Clean Up (Optional)

```bash
# Delete the service and deployment
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml

# Stop minikube
minikube stop
```

## Expected Deliverables

1. The `deployment.yaml` and `service.yaml` files
2. Screenshots showing:
   - Pods running (`kubectl get pods`)
   - Services running (`kubectl get services`)
   - Scaled deployment (`kubectl get pods` after scaling)
   - Application running in browser
   - Output of `kubectl describe pod`

## Troubleshooting

### Docker Daemon Not Running
If you see an error about connecting to Docker daemon:
```
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

Ensure Docker Desktop is running or try a different driver:
```bash
# Try with Hyper-V
minikube start --driver=hyperv

# Or try with VirtualBox
minikube start --driver=virtualbox
```

### Connection Errors
If kubectl can't connect to the cluster, reset your configuration:
```bash
mkdir -p %USERPROFILE%\.kube
echo > %USERPROFILE%\.kube\config
minikube start
```
