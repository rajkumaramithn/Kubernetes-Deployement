Kubernetes Microservices Deployment using Minikube

Objective
Deploy a microservices application on Kubernetes using Minikube, ensuring proper service communication and configuration.


Microservices	    Port	    Purpose
User Service	    3000	    Provides user-related APIs
Product Service	    3001	    Provides product-related APIs
Order Service	    3002	    Provides order-related APIs
Gateway Service	    3003	    Acts as the API gateway and communicates with other services

The main objectives of this project are:
•	Containerize the Node.js microservices using Docker. 
•	Create Kubernetes Deployment resources for all four services. 
•	Create Kubernetes ClusterIP Services for internal service discovery. 
•	Configure resource requests and limits. 
•	Configure environment variables for service-to-service communication. 
•	Configure liveness and readiness probes. 
•	Deploy all services on a local Minikube Kubernetes cluster. 
•	Verify that all microservices are running. 
•	Test service accessibility using Kubernetes port forwarding. 
•	Validate service communication using Kubernetes logs and API responses.

Application Architecture
The application contains four services:

 
Each service runs inside its own Kubernetes Pod.

Kubernetes Services provide stable DNS names that allow the microservices to communicate with one another.

Repository Structure
 


Prerequisites
The following software are required:
•	Docker Desktop 
•	Minikube 
•	kubectl 
•	Node.js 
•	Visual Studio Code 
•	
Docker Desktop is used as the container runtime and Minikube is used to provide the local Kubernetes cluster.

Execution Procedure
Installation & Verification of Docker Desktop :
Before executing minikube , we have to download the docker desktop application and install it on the local machine. 
To verify that Docker has been installed and is running , Open Powershell with elevated privileges and run the following command. 
docker  - -version
docker  info
 

Verification of Minikube installation :
Check the installed Minikube version by running 
minikube version

Start Minikube using Docker as the driver
minikube start  - -driver=docker

Verify the Minikube cluster
minikube status

 
 

Verifying Kubernetes 
Check the Kubernetes noes by running 
Kubectl get nodes
 
This confirms that the local Kubernetes cluster is operational.









Creating the Deployments and Submissions Directories 
 
 

Creating the YAML files for all four services
 
 
 
 
 
Buiding the Docker images and verifying them
Navigate into the Microservices Directory of the Project and run the following command to build the docker images of all the services using the docker file of each service individually.
docker build -t user-service:latest ./user-service
docker build -t product-service:latest ./product-service
docker build -t order-service:latest ./order-service
docker build -t gateway-service:latest ./gateway-service
docker images
 

Load Docker Images into Minikube
Because the Kubernetes cluster is running inside Minikube, the locally built Docker images need to be made available to Minikube.
 
This makes the application images available to the Minikube Kubernetes node.’
This creates four Service Manifests 

Starting and deploying the Kubernetes Services
To start the Kubernetes we use :
minikube start
 


Navigate into the submission directory and run the following 
kubectl apply – f deployements/
kubectl apply – f services/
 
Run “ kubectl get deployments ” to verify the creation.

Verifying the Pods 
Check all application Pods by running the command 
kubectl get pods -w
 

Verifying the Kubernetes Services
To do this run :
kubectl get services
 
Test Services Using API Testing & Port Forwarding
kubectl port-forward service/user-service 3000:3000
kubectl port-forward service/product-service 3001:3001
kubectl port-forward service/order-service 3002:3002
kubectl port-forward service/gateway-service 3003:3003

http://localhost:3000/health
http://localhost:3001/health
http://localhost:3002/health
http://localhost:3003/health
    
    



Checking Application Logs

The logs can be viewed by running the following:

kubectl logs deployment/gateway-service
kubectl logs deployment/user-service
kubectl logs deployment/product-service
kubectl logs deployment/order-service

 


Troubleshooting

ImagePullBackOff / ErrImagePull

If a pod shows “ ImagePullBackOff “ verify that the image exists by : 

docker images 

Then load it into Minikube:

minikube image load user-service:latest

Repeat for the other services.

If this does not work, go into the docker image of each service and set 

imagePullPolicy: Never

in the YAML file.




  


CrashLoopBackOff

Check the Pod logs:

kubectl logs <pod-name>

A common cause during development was a missing npm start script.

The package.json should contain:
"scripts": {
  "start": "node app.js"
}


After modifying the application or package.json, rebuild the Docker image and reload it into Minikube:

docker build -t user-service:latest ./user-service

minikube image load user-service:latest


Bonus – Ingress Implementation

Creating the ingress directory and the ingress YAML file

We create the /ingress directory within the submissions directory and create the ingress.yaml file. 
Creating the ingress addon and verifying the running state

Add the ingress add on to the minikube by running :

minikube addons enable ingress

and verify it being ready by running :

kubectl get pods -n ingress-nginx

  


Making the change in the hosts file to map the minikube ip to the microservices.local

Verifying the minikube ip :

 

Mapping the ip to microservices.local
Notepad “C:\Windows\System32\drivers\etc\hosts”

 

Verify that ingress is running

 

Start the minikube Tunnel

Run the command :
minikube tunnel

 

Executing the ingress deployment via microservices.local

Run the command on any local browser:

http://microservices.local/api/users/health

This concludes the successful deployment via ingress.




Conclusion

This project successfully demonstrates the deployment of a Node.js microservices application on a local Kubernetes cluster using Minikube.

The implementation includes:
•	Docker containerization 
•	Four independent Node.js microservices 
•	Kubernetes Deployments 
•	Kubernetes ClusterIP Services 
•	Kubernetes service discovery 
•	Environment variable configuration 
•	Liveness probes 
•	Readiness probes 
•	Local Minikube deployment 
•	Port-forward based service testing 
•	Application log validation 

Additionally, the optional Ingress configuration was successfully implemented, enabling unified external access through a single host (microservices.local) with proper path-based routing and URL rewriting. This demonstrated how real-world applications can be exposed cleanly and efficiently using an API gateway pattern.
Overall, the project highlights a complete end-to-end workflow—from containerization to orchestration and external access—providing a strong foundation in Kubernetes-based microservices deployment and management.

The completed deployment provides a foundation for running the microservices application in Kubernetes and demonstrates the fundamental concepts of container orchestration, service discovery, health monitoring, and resource management.


