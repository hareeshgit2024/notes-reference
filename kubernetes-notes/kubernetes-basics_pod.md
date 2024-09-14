**BASICS OF KUBERNETES**

In the modern era, deploying applications into production is streamlined through the use of automation, continuous integration (CI), and continuous deployment (CD) pipelines. These pipelines ensure that code changes can be tested, built, and deployed quickly and efficiently, reducing the risk of errors and downtime. Containerization tools like `Docker` play a pivotal role by packaging applications and their dependencies into portable, lightweight `containers`. This ensures consistency across different environments—whether in development, staging, or production—and makes it easier to create production-ready builds. 

However, managing Docker images alone in a production environment can be challenging, especially at scale. This is where `container orchestration` tools, such as `Kubernetes` [`Originated from Google, but now managed by CNCF (Cloud Native Computing Foundation)`], come into play. 

Orchestration is critical because it automates the deployment, scaling, and management of containerized applications across a distributed environment, ensuring high availability, fault tolerance, and efficient resource utilization.

---
<details>
<summary>Real Life example</summary>
.
  
Imagine a large e-commerce platform that serves millions of users daily. To ensure the platform is always up and running smoothly, the engineering team uses Docker to containerize each microservice (e.g., payment, inventory, and user authentication). The platform’s CI/CD pipeline automates the process of building and deploying Docker images. In production, these containers are not manually managed; instead, Kubernetes orchestrates them across multiple cloud servers. Kubernetes automatically scales services during high traffic, performs health checks, and restarts any failed containers. This orchestration ensures that the platform remains responsive, resilient, and capable of handling spikes in demand without downtime, illustrating the synergy between Docker and Kubernetes in modern production environments.
</details>

---

In a scenario where a proper orchestration service like Kubernetes is not available, managing containerized applications manually can lead to a host of operational issues, especially as the system scales.

- Manual Scaling: Without orchestration, scaling the application in response to traffic spikes requires manual intervention. If traffic suddenly surges, the team has to manually start additional containers to handle the load. This introduces delays and can result in service outages or slow performance while waiting for the manual process to complete.

- Lack of Fault Tolerance: If a container crashes, there is no automated way to detect and restart it. This could lead to downtime for critical services. For example, if the payment service crashes, users trying to make purchases would experience failures, leading to lost revenue and a poor user experience.

- Resource Inefficiency: Without proper orchestration, containers might not be optimally distributed across the available infrastructure. Some servers could be underutilized, while others are overloaded. This inefficient resource allocation leads to higher operational costs and degraded performance.

- Configuration Drift: As the number of services grows, keeping track of which versions of containers are deployed on different machines can become difficult. Without automated orchestration, inconsistencies between environments (development, staging, production) can arise, making it harder to replicate and fix issues.

- Service Discovery: In a complex system with many interdependent services, keeping track of where each service is running can be a challenge. Without an orchestrator handling service discovery, developers may have to manually update configurations or load balancers to ensure services can communicate, introducing potential for errors.

- No Load Balancing: Without orchestration, requests might not be distributed evenly among containers, leading to some instances being overwhelmed while others are idle. This can create bottlenecks and performance degradation.

---
<details>
<summary>Real Life example</summary>
.
  
Imagine a pizza restaurant that’s trying to keep up with a huge influx of customers. Instead of having a manager to orchestrate everything, each employee does their own thing. The pizza makers just start tossing dough without checking if they have enough ingredients, and nobody knows how many pizzas to make. One pizza maker finishes first and has nothing to do, while the others are juggling five orders at once. Chaos!

The delivery guys don’t know which pizzas are ready, so they keep running to the kitchen, grabbing random boxes, and sometimes even delivering half-baked pizzas because they didn't check. When a pizza gets dropped on the floor, no one picks up the slack to make a new one—they just stare at the mess until someone randomly decides to fix it. The customers are confused, hungry, and furious because nobody’s managing this circus.

This is what happens when you don’t have an orchestration service in production. Without something like Kubernetes to manage, balance, and keep things running smoothly, you’ve got containers (the pizza makers) running wild, no automatic recovery when things break (like the dropped pizza), and a lot of unhappy customers (users) waiting on their orders. Total disaster!
</details>

---

**ARCHITECTURE OVERVIEW**

Kubernetes (K8s) architecture is designed to manage containerized applications efficiently across a cluster of machines. It consists of two primary components: the Control Plane and Worker Nodes.

![image](https://github.com/user-attachments/assets/cde91f56-f162-43d1-84fa-7977321bdff6)


**Control Plane**: This is the brain of Kubernetes and manages the overall cluster. Key components include:

- API Server: Acts as the gateway for all administrative tasks and interacts with the K8s cluster.
- etcd: A distributed key-value store that holds the cluster’s configuration data.
- Scheduler: Assigns containers (Pods) to nodes based on resource availability and requirements.
- Controller Manager: Monitors the cluster’s state and ensures the desired state matches the actual state.

**Worker Nodes**: These are the machines where containers run. Each node has:

- Kubelet: An agent that communicates with the control plane, ensuring containers are running in Pods.
- Container Runtime: Runs the containers (e.g., Docker, containerd).
- Kube-proxy: Handles networking, ensuring communication between Pods.
- Kubernetes uses a declarative model, where you define the desired state (e.g., number of replicas), and the control plane ensures the cluster achieves and maintains that state, providing scalability, fault tolerance, and automated management.

---
<details>
  <summary>Real Life example</summary>

.
Imagine a food delivery company running a mobile app. Each part of the system—order processing, delivery tracking, and payments—runs as a separate microservice. Kubernetes (K8s) acts as the control center that manages these services across multiple servers (nodes).

In this setup, the Control Plane is like the headquarters. The API Server is the communication hub where all requests pass through. The Scheduler ensures new deliveries (containers) are assigned to the right drivers (nodes) based on available resources. The Controller Manager ensures that if any delivery driver fails, another one is quickly sent out (restarts failed containers). etcd acts as the company's database, storing all operational data like which deliveries are in progress.

On the ground, the Worker Nodes (servers) are the actual drivers. Each driver has a Kubelet (an agent) that makes sure the delivery (service) is running properly. The Container Runtime (like Docker) runs the microservices, while Kube-proxy handles communication between them, ensuring orders flow smoothly from the customer to the restaurant, the delivery, and payment systems.

Kubernetes orchestrates all these moving parts, ensuring smooth operations, scaling up when there’s high demand, and recovering quickly from any issues.
</details>

---

**INSTALLATIONS**

- Kubectl : https://kubernetes.io/docs/tasks/tools/
- Minikube : https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download

**DOCKER - Overview**

https://github.com/hareeshgit2024/notes-reference/blob/dev/docker-notes/docker-notes.md


**Minikube**

Install minikube in windows => https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2Fchocolatey

Command to install minikube using chocolatey => choco install minikube
Start the minikube server => 
 minikube start
 minikube status
 
 minikube docker-env

 ![image](https://github.com/user-attachments/assets/bd54cb02-a0c2-401c-a840-d11e908e477d)
 if you want your docker CLI to point to the docker daemon, you can run the last command mentioned in the above screenprint.

To view the minikube dashboard => 
    minikube dashboard

kubectl version --client
kubectl cluster-info

**POD**

A `Pod` is the smallest deployable unit in Kubernetes, representing one or more containers that share the same network and storage. 
Each `Container` within a Pod runs a specific application or microservice, isolated from others but able to communicate within the Pod. 
A `Sidecar (or Helper container)` is a secondary container in the Pod that assists the main container by performing auxiliary tasks, such as logging, monitoring, or proxying.

For example, a logging sidecar can capture and send logs while the main container focuses solely on its primary application. Both containers share resources and can work together efficiently.

---

<details>
  <summary>Real Life example</summary>
  

Imagine a tea shop that has a counter for serving tea and a small station for keeping the shop clean. In Kubernetes, this setup can be thought of as a Pod. The main container is like the tea-serving counter where the actual tea is prepared and served to customers, representing the core application or service.

However, the tea shop also needs a helper—a cleaning station—to ensure the counter stays clean and organized. This helper station is like a Sidecar container in the Pod. The sidecar (or helper) assists by performing tasks like cleaning (logging, monitoring), without interrupting the tea preparation process.

Both the tea counter (main container) and the cleaning station (sidecar) work in the same Pod (shop), sharing the same environment and resources, like water and electricity (network and storage). The combination ensures the tea shop runs smoothly, with the helper taking care of essential background tasks while the main service remains focused on serving customers.  
</details>

---

![image](https://github.com/user-attachments/assets/1e642b64-61f0-4908-bd72-f98bee037b79)

When a Pod is created or accessed in Kubernetes, several real-time execution steps are involved to run the containers inside it:

**Pod Scheduling**: When a Pod is requested (e.g., via a YAML configuration), the Kubernetes Scheduler identifies a suitable Node based on available resources (CPU, memory) and the Pod's requirements (like affinity or tolerations).

**Container Creation**: Once scheduled, the Kubelet on the assigned Node receives instructions from the API Server to create the Pod. The Kubelet pulls the necessary container images (e.g., from Docker Hub) using the Container Runtime (like Docker or containerd).

**Network Setup**: Kubernetes assigns an IP address to the Pod, and sets up the networking so that containers in the Pod can communicate with each other via localhost. Kube-proxy handles routing so the Pod can communicate with other Pods or services within the cluster.

**Container Startup**: The containers within the Pod start running based on the image's instructions (like entry point or command). They may also share storage volumes if defined in the Pod configuration.

**Monitoring & Health Checks**: The Kubelet continuously monitors the health of the containers, restarting any container that fails or exits unexpectedly, as defined by the Pod's liveness or readiness probes.

**Pod Access**: Once running, the containers can be accessed internally within the cluster or externally (if exposed via a Service or Ingress).

Kubernetes ensures that containers in the Pod remain operational, scaling or restarting as needed.

**Writing a POD**

Get ready to start writing for creating a pod.

First let's make sure the minikube is up and running with command `minikube status`.

List all details which we have defined within the kubernetes cluster `kubectl get all`

On executing this command, we get the following `service/kubernetes` which the Rest API exposed by the kubernetes cluster.
Whenever we trigger a kubectl command, kubectl is automatically posting the command to this API rest endpoints. 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp  
spec:
  containers:
  - name: webapp
    image: richardchesterwood/k8s-fleetman-webapp-angular:release0
```
Create the above yaml file and save it as pod_one.yml. Make sure you navigate to the directory where the yml file is saved and execute the command 

`kubectl apply -f pod_one.yml`

Once the command is executed successfully, this should give the response as `pod/webapp created`

Now it should display

```
kubectl get all
NAME         READY   STATUS    RESTARTS      AGE
pod/nginx    1/1     Running   1 (28d ago)   34d
pod/webapp   1/1     Running   0             5m29s
```

If all working fine, fetch the minikube ip with the command `minikube ip`

On trying to access the above IP from a browser, we will see the page is not accessible. This is because the pod is not accessible to the external world.

![image](https://github.com/user-attachments/assets/6f868c73-fe0c-4904-8d95-3e5d3766b957)

```bash
Common commands on K8S Pod

#Lists all Pods in the current namespace.
kubectl get pods

#Shows detailed information about a specific Pod.
kubectl describe pod <pod-name>

#Retrieves the logs from a Pod’s container.
kubectl logs <pod-name>

#Executes a command inside a running Pod.
kubectl exec <pod-name> -- <command>

#Forwards a local port to a Pod.
kubectl port-forward <pod-name> <local-port>:<pod-port>

#Deletes a specific Pod.
kubectl delete pod <pod-name>

#Scales the number of Pods for a deployment.
kubectl scale --replicas=<num> deployment/<deployment-name>

#Copies files between a Pod and local machine.
kubectl cp <pod-name>:<source-path> <destination-path>

#Creates or updates Pods based on a YAML configuration file.
kubectl apply -f <yaml-file>

#Retrieves the YAML definition of a specific Pod.
kubectl get pod <pod-name> -o yaml

#Displays detailed information about the <pod-name> pod, including its IP address, node it is running on, and other extended details.
kubectl get pod <pod-name> -o wide 

#Restarts all Pods in a deployment.
kubectl rollout restart deployment/<deployment-name>

#Shows resource usage (CPU/memory) for Pods.
kubectl top pod

```
