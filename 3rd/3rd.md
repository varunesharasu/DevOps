# Installing and setting up Kubernetes Minikube

## Update system packages and Install dependencies
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget apt-transport-https conntrack socat
```
![image](https://github.com/user-attachments/assets/0019f77d-60d9-44fb-9cd9-030f88256d20)

## Install Docker, Kubectl and Minikube
### Install Docker
```bash
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
docker --version
```
### Install Kubectl
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```
### Install Minikube
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
```
![image](https://github.com/user-attachments/assets/f16981cd-567f-4641-bf39-caf64312b7ee)
![image](https://github.com/user-attachments/assets/f98aef39-c1a1-4941-8e16-fdeede4d2170)

### Fix Docker setup
```bash
sudo usermod -aG docker $USER
reboot
```

### Verify Installation after reboot
```bash
docker run hello-world
```
![image](https://github.com/user-attachments/assets/7f55cdfd-b47d-4fec-93c5-59bebbeee6fb)


## Start and open Minikube
```bash
minikube start --driver=docker
minikube dashboard
```
![image](https://github.com/user-attachments/assets/70a3995c-436e-472d-81ea-90920026d8e0)
![image](https://github.com/user-attachments/assets/8d49ff13-7975-4253-8983-37b241648eca)

## Deployinig the docker image from dockerhub

```bash
mkdir docker
nano  Dockerfile
npm init -y
```

![image](https://github.com/user-attachments/assets/6dd49514-1000-461c-b688-b99bd2900b75)

```bash
npm init -y // again if the previous command installed npm
cd ..
docker pull sanjai4334/docker:latest . // replace with your docker uid/repo:image_tag
cd docker
```

![image](https://github.com/user-attachments/assets/f43cdced-7f44-4021-8d53-51cff5afc63a)

```bash
docker build -t sanjai4334/docker/latest // replace with your docker uid/repo:image_tag
docker ps -a
```

![image](https://github.com/user-attachments/assets/8e05a11b-bceb-4a6e-8a2a-ecf37a760fa7)

```bash
sudo nano nginx-deployment.yaml
kubectl apply -f nginx-deployment.yaml
sudo nano service.yaml
kubectl apply -f service.yaml
kubectl get pods
minikube get svc my-app
minikube service my-app --url
curl <url>
```

![image](https://github.com/user-attachments/assets/0b3f729e-204c-4f20-ba2f-af829eace370)

 - open the url in browser

![image](https://github.com/user-attachments/assets/737839dc-6c3a-45a8-a937-bf21a6a78e14)


## Config file codes
Dockerfile
```groovy
# Use an official Node.js runtime as a base image
FROM node:18

# Set the working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package.json ./
RUN npm install

# Copy the rest of the application
COPY . .

# Expose port 3000 and start the app
EXPOSE 3000
CMD ["npm", "start"]
```

nginx-deployment.yaml
```groovy
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: sanjai4334/docker:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
```

service.yaml
```groovy
apiVersion: v1
kind: Service
metadata:
  name: my-app
  namespace: default
spec:
  type: NodePort  # Ensures external access via a specific port
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80       # Service port inside the cluster
      targetPort: 80  # The container's port
      nodePort: 30391   # Externally accessible port
```