
**DEPLOYMENT**

Kubernetes deployment automates the process of managing and scaling applications in a cluster. It involves creating a Deployment object that defines how your app will be managed, including the number of replicas, the container image, and updates.

Defining a deployment object

The deployment is specified in a YAML file, which includes details like the container image, replicas, and port configuration

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:1.0
        ports:
        - containerPort: 80

```
Save the above file as `deployment.yml`, and run the kubectl command `kubectl apply -f deployment.yaml` to deploy the application. Kubernetes ensures the desired number of replicas are running.
You can easily scale by updating the replica count using `kubectl scale deployment my-app --replicas=5`.

Use `kubectl get deployments` to check deployment status and `kubectl describe deployment my-app` for details. Kubernetes ensures high availability, restarts failed pods, and load balances traffic.

**POD and DEPLOYMENT**

In Kubernetes, a Deployment is a higher-level abstraction for managing and scaling applications, while a Pod is the basic execution unit that runs containers.

1. Pod:
- Definition: A Pod is the smallest and simplest Kubernetes object, representing one or more containers that share the same network namespace and storage.
- Purpose: Pods are created to host your application container(s), along with their runtime environment, like storage volumes and networking.
- Lifespan: Pods are ephemeral. They can die and won’t be automatically replaced if there’s no controller managing them (like a Deployment).
- Limitations: Pods don't self-heal or scale by themselves. If a pod crashes, Kubernetes won't automatically replace it unless it's managed by a higher abstraction (such as a Deployment).

2. Deployment:
- Definition: A Deployment is a Kubernetes controller that manages Pods and provides declarative updates to them.
- Purpose: The Deployment specifies the desired state of an application, such as how many replicas (Pods) should run, the container image to use, and how updates should be rolled out.
- Self-Healing: If a pod fails or gets deleted, the Deployment controller automatically replaces it to maintain the desired state.
- Rolling Updates: Deployments support rolling updates, which allow you to update an app’s container image without downtime. It rolls out changes progressively, ensuring availability during updates.
- Scaling: Deployments enable easy horizontal scaling by specifying how many replicas of the pod should run simultaneously.
  
Key Differences:

Pod: Represents a single instance of your running application, but it’s not self-managed.
Deployment: Manages multiple replicas of Pods, ensures availability, handles rolling updates, and provides scaling functionality.

**Suggested Best practices associated with Deployment declaration and execution**

When defining a Deployment object, would request to adhere to the below set of best practices

1. Use versioned container images
   Make sure to specify a versioned tag for your container images (e.g., my-app:v1.0) instead of latest. This ensures that your deployments are predictable and avoid unintended updates when the latest image changes.
2. Set proper resource requests and Limits
   Define resource requests (minimum resources needed) and limits (maximum resources allowed) for CPU and memory in your Deployment spec. This prevents Pods from consuming too many cluster resources or being starved of them.

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "500m"
  limits:
    memory: "512Mi"
    cpu: "1"

```
3. Use health checks (Liveness and Readiness probes)
     Liveness Probe ensures that Kubernetes restarts a container if it gets stuck or crashes.
     Readiness Probe ensures that Kubernetes routes traffic to a Pod only after it’s ready to handle requests.
   
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```
4. Configure Rolling Updates with a Strategy
   Use a rolling update strategy to prevent downtime when updating your application. Control the speed of the rollout by configuring maxSurge (extra Pods created during update) and maxUnavailable (Pods that can be unavailable).
```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 0
```
5. Set up Autoscaling
   Configure Horizontal Pod Autoscaler (HPA) to automatically adjust the number of Pods based on CPU/memory usage or custom metrics. This improves availability and optimizes resource utilization.
   `kubectl autoscale deployment my-app --cpu-percent=80 --min=2 --max=10`
6. Use Labels and Selectors
   Apply labels to your Pods for better organization and management. Ensure that the selector in the Deployment matches these labels to correctly identify Pods managed by the Deployment.
```yaml
labels:
  app: my-app
  environment: production
```
7. Ensure Immutable Configurations
   Store configurations (e.g., environment variables, secrets, and config maps) outside the container image using ConfigMaps and Secrets to make changes without modifying the deployment.
```yaml
envFrom:
  - configMapRef:
      name: my-app-config
```
8. Enable Pod Disruption Budgets (PDBs)
   Define a Pod Disruption Budget to limit the number of Pods that can be down during voluntary disruptions (like node maintenance) and ensure high availability.
```yaml
spec:
  minAvailable: 2
```
9. Manage Secrets Securely
   Store sensitive data, like API keys and passwords, in Kubernetes Secrets, not directly in the deployment YAML or container image.

<details>
  <summary>Real life example - Pods, Deployment, Service</summary>

  Let’s use the analogy of a Tea Restaurant to explain Pods, Deployments, and Services in Kubernetes, relating each concept to real-life operations in the restaurant.

1. Pods (Tea Chef and Tea Setup)
Real Life: Think of a Pod as a chef who makes tea at the restaurant. The pod is like the chef’s workstation, including all the ingredients, equipment, and setup (teapots, kettles, tea leaves, water, etc.).

The chef (container) works within this station (Pod) to prepare the tea.
If the chef messes up the tea (app failure), the chef is replaced, but the restaurant doesn’t hire a whole new team for every small failure. The kitchen just gets a new chef and starts over (Kubernetes restarts the pod if it fails).
Kubernetes: A Pod in Kubernetes is the smallest deployable unit that contains one or more containers. It holds the application (e.g., a container running the tea-making software), its environment, and dependencies (like volumes, configs).

2. Deployment (Tea Management System)
Real Life: A Deployment is like the restaurant’s management system. It handles operations like how many chefs (pods) are needed to serve tea, replaces any chef who gets sick (self-healing), and scales the number of chefs up or down based on the crowd.

If the restaurant gets more customers, the manager hires more chefs to keep up with demand (scaling the deployment).
If a chef (pod) starts making bad tea, the manager replaces that chef without shutting down the whole restaurant (rolling updates).
Kubernetes: A Deployment manages multiple instances of Pods, ensuring the desired number of them are running, healing any unhealthy Pods, and performing updates smoothly. It can scale the application by increasing or decreasing the number of pods (replicas).

3. Service (Waiters Serving Tea)
Real Life: A Service is like the waiters in the restaurant who serve tea to customers. Customers don’t care which chef made the tea—they just need their tea served efficiently.

The waiter (service) knows where to go to get tea (which pod/chef is ready) and makes sure the customer gets their tea.
If one chef (pod) finishes a batch of tea and another is still working, the waiter balances the load by picking the tea from the available chefs.
The waiter serves customers whether the restaurant is busy or not. Customers don't need to know if there are 1 or 10 chefs—they just receive the tea from the waiter.
Kubernetes: A Service in Kubernetes abstracts access to the pods. It provides a stable IP address and load balances traffic across the available Pods. Clients don’t interact directly with Pods, but through the Service, ensuring that traffic is directed to the correct and healthy Pods.

Example Scenario:
Chefs (Pods): You have 3 chefs in the kitchen, each making tea. Each chef has their own station with the required setup to make tea. One day, a chef gets sick (pod failure), so a new chef is brought in to replace them automatically.

Restaurant Management (Deployment): The restaurant manager monitors how busy the restaurant is. If too many people are ordering tea (increased load), the manager brings in more chefs (scales the deployment). If tea sales are low, the manager reduces the number of active chefs to save resources. The manager also ensures that only healthy chefs are working, replacing them if they become unable to perform (self-healing).

Waiters (Service): Customers don’t go directly to the kitchen to pick their tea. Instead, waiters serve them. The waiter is like the Service—customers (clients) don’t care which chef (Pod) made the tea; they just want their tea delivered. The waiter ensures every customer gets served tea from one of the available chefs, regardless of who is available.

How They Relate in Kubernetes:
Pods (Chefs) are where the actual work happens, making tea (running your application).
Deployment (Management) oversees the operation, ensuring the right number of Pods (chefs) are always available, replacing sick ones, and scaling up or down as needed.
Service (Waiter) is the point of access for customers (clients) to get their tea (services). It handles routing to the right Pods (chefs) and balances the load among them.

</details>
A sample deployment yaml file for reference

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
    environment: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
        environment: production
    spec:
      containers:
      - name: my-app-container
        image: my-app:v1.0 # Versioned image
        ports:
        - containerPort: 8080
        resources:
          requests: # Resource requests and limits
            memory: "256Mi"
            cpu: "500m"
          limits:
            memory: "512Mi"
            cpu: "1"
        livenessProbe: # Liveness Probe to check if the container needs to be restarted
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe: # Readiness Probe to ensure container is ready to serve traffic
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
        envFrom: # Environment variables from ConfigMap
        - configMapRef:
            name: my-app-config
        env: # Environment variables from Secrets (for sensitive data)
        - name: API_KEY
          valueFrom:
            secretKeyRef:
              name: my-app-secret
              key: api-key
      restartPolicy: Always

  strategy: # Rolling update strategy
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0

---
apiVersion: autoscaling/v1 # Horizontal Pod Autoscaler for scaling based on CPU utilization
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80

---
apiVersion: policy/v1 # Pod Disruption Budget to ensure high availability
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

**LIVENESS and READINESS probe**

Liveness Probe and Readiness Probe are essential features in Kubernetes that help ensure the health and availability of your application. Both work by periodically checking the status of a container, but they serve different purposes:

1. **Liveness Probe**: Ensures the container is "alive."
_Purpose_: It checks whether the application inside the container is still running. If the probe fails, Kubernetes restarts the container.
_When is it used?:_
If the application gets stuck in a deadlock (e.g., the process is running but no longer doing useful work), the Liveness Probe detects this and triggers a restart.
Useful for applications that can enter a non-responsive state and need a full restart to recover.

Types of Liveness Probes:

HTTP Probe: Sends an HTTP GET request to the container and expects a successful response (2xx or 3xx HTTP status).
TCP Probe: Establishes a TCP connection to the specified port. If the connection is successful, the probe passes.
Exec Probe: Runs a command inside the container. If the command returns a 0 status code, the container is considered alive.

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10
```
The Liveness Probe checks the /health endpoint every 10 seconds after an initial delay of 30 seconds. If it fails, Kubernetes assumes the container is unhealthy and restarts it.

2. **Readiness Probe**: Ensures the container is "ready" to serve traffic.
_Purpose_: It checks whether the container is ready to accept incoming traffic. If the probe fails, the container is temporarily removed from the list of endpoints for the service, meaning it won't receive traffic until it becomes "ready."
_When is it used?:_
During the startup of applications that need some time to initialize (e.g., loading configurations, database connections, etc.).
For applications that may temporarily become unresponsive or unavailable but don’t need a restart (e.g., during heavy load or resource-intensive operations).

Types of Readiness Probes:

HTTP Probe: Similar to the Liveness Probe, it sends an HTTP GET request to check if the application is ready to handle requests.
TCP Probe: Attempts a TCP connection to the container.
Exec Probe: Executes a command inside the container to verify readiness

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```
The Readiness Probe checks the /ready endpoint every 5 seconds after an initial delay of 10 seconds. If it fails, the container is temporarily marked "not ready," and traffic is routed away from it until it becomes ready again.

Common kubectl commands used associated to Deployment

```
# Lists all deployments in the current namespace.
kubectl get deployments

# Lists details of a specific deployment by name.
kubectl get deployment <deployment-name>

# Displays detailed information about a specific deployment, including pod status, events, and conditions.
kubectl describe deployment <deployment-name>

# Creates or updates a deployment using a YAML manifest file.
kubectl apply -f <deployment.yaml>

# Deletes a specific deployment by name.
kubectl delete deployment <deployment-name>

# Scales a deployment to the specified number of replicas.
kubectl scale deployment <deployment-name> --replicas=<number>

# Updates the image of a deployment container to a new version.
kubectl set image deployment/<deployment-name> <container-name>=<new-image>

# Checks the status of a rolling update for a deployment.
kubectl rollout status deployment/<deployment-name>

# Pauses a deployment to stop further rollouts.
kubectl rollout pause deployment/<deployment-name>

# Resumes a paused deployment, allowing further rollouts.
kubectl rollout resume deployment/<deployment-name>

# Rolls back a deployment to the previous revision.
kubectl rollout undo deployment/<deployment-name>

# Shows the revision history of a deployment.
kubectl rollout history deployment/<deployment-name>

# Limits the number of historical revisions to keep for a deployment.
kubectl rollout history deployment/<deployment-name> --revision=<revision-number>

# Exposes a deployment as a Kubernetes service.
kubectl expose deployment <deployment-name> --type=<service-type> --port=<port> --target-port=<container-port>

# Automatically scales a deployment based on resource usage (CPU).
kubectl autoscale deployment <deployment-name> --min=<min-replicas> --max=<max-replicas> --cpu-percent=<cpu-percent>

```
