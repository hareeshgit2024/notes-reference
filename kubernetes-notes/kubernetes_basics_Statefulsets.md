
**STATEFULSETS**

In Kubernetes, StatefulSets are a specialized type of controller used to manage stateful applications. 
They provide features that are particularly useful for applications that require persistent storage, stable network identities, and ordered deployment and scaling. 

Here are the key features of StatefulSets:

- **Stable Network Identity**: Each pod in a StatefulSet has a unique and stable hostname, which is based on the StatefulSet's name and the pod's ordinal index (e.g., web-0, web-1, etc.).
  This makes it easier for applications to discover and communicate with one another.

- **Stable Storage**: StatefulSets allow you to use persistent storage volumes that are retained across pod restarts.
  Each pod in a StatefulSet can be associated with its own persistent volume, ensuring that the data is not lost when the pod is deleted or restarted.

- **Ordered Deployment and Scaling**: Pods in a StatefulSet are deployed and scaled in a specific order.
  This means that they are created one at a time, and each pod must be running and ready before the next one is started. This is particularly useful for applications that rely on order, such as databases.

- **Graceful Deletion**: When a StatefulSet is deleted, the pods are terminated in reverse order, ensuring that higher-numbered pods are deleted before lower-numbered ones.
  This allows for graceful shutdowns and ensures that applications can handle termination correctly.

- **Headless Services**: StatefulSets typically use a headless service to control the network identities of the pods.
  This allows the pods to be accessed directly by their hostname, rather than through a load balancer.

**Use Cases**

StatefulSets are commonly used for applications that require stable identities and persistent storage, such as:

- Databases (e.g., MySQL, PostgreSQL)
- Distributed file systems (e.g., Ceph, GlusterFS)
- Message queues (e.g., Kafka)

Sample YAML file

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: mypassword
  volumeClaimTemplates:
  - metadata:
      name: mysql-persistent-storage
    spec:
      accessModes: ["PersistentVolumeClaim"]
      resources:
        requests:
          storage: 1Gi
```

In the above example, a _StatefulSet_ named `mysql` is created with `three replicas`. Each replica gets its own persistent storage volume.

**Recommended Best Practices associated with Statefulset are**

Important to consider best practices and security features to ensure your applications are reliable, scalable, and secure. Here are some best practices and security features to keep in mind:

**Best Practices**

1. Use Persistent Volumes:

- Always use Persistent Volumes (PVs) for data storage to ensure data persistence across pod restarts.
- Define appropriate Storage Classes to manage your storage according to performance and cost needs.
  
**2. Configure Resource Requests and Limits:**

- Set resource requests and limits for CPU and memory for your StatefulSet pods. This helps Kubernetes manage resources efficiently and can prevent resource contention.

**3. Manage Pod Disruption Budgets:**

- Define `PodDisruptionBudgets` to ensure that a minimum number of pods remain available during voluntary disruptions (e.g., maintenance).

**4.Rolling Updates:**

- Use the rolling update strategy to update StatefulSet pods, ensuring that pods are updated one at a time and that the application remains available during the update.

**5. Headless Services:**

- Utilize headless services for your StatefulSet to manage network identities and enable direct access to each pod by its hostname.

**6. Readiness and Liveness Probes:**

- Implement readiness and liveness probes to ensure that traffic is only sent to healthy pods and that unhealthy pods are restarted.

**7. Data Backup and Recovery:**

- Establish a regular backup and recovery strategy for your stateful applications to protect against data loss.

**8. Horizontal Pod Autoscaling:**

- Consider using Horizontal Pod Autoscalers (HPA) to automatically scale the number of pod replicas based on CPU utilization or other metrics.

**Security Features**

**1. Network Policies:**

- Use Network Policies to control traffic between StatefulSet pods and other services, restricting access to only necessary communication.

**2. Role-Based Access Control (RBAC):**

- Implement RBAC to define permissions for users and service accounts accessing your StatefulSets and related resources.

**3. Secrets Management:**

- Use Kubernetes Secrets to manage sensitive information (e.g., database credentials) and mount them as environment variables or files within your pods.

**4. Pod Security Standards:**

- Enforce pod security standards using tools like PodSecurityAdmission or third-party solutions like OPA (Open Policy Agent) to ensure that your StatefulSet pods adhere to security policies.

**5. Avoid Running as Root:**

- Configure your containers to run as a non-root user by specifying a user in the container definition. This limits the impact of a security breach.

**6.Use Image Scanning and Vulnerability Assessments:**

- Regularly scan container images for vulnerabilities using tools like Clair, Trivy, or Anchore. Ensure that only trusted images are used in your StatefulSets.

**7. Limit Privileges with Security Contexts:**

- Define a security context for your StatefulSet pods to limit privileges, such as disallowing privilege escalation and restricting capabilities.
  
**8. Regularly Update and Patch:**

- Keep your Kubernetes cluster, container images, and application dependencies updated to mitigate security vulnerabilities.

Here we are supplying a simple single yaml file that includes Deployment, Statefulsets, PersistantVolumes, PersistantVolumeClaim and associated services.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/mysql

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: mypassword
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: mysql-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi

---
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
    - port: 3306
  clusterIP: None  # Headless service
  selector:
    app: mysql

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nginx:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: webapp
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
  selector:
    app: webapp
```

PersistentVolume (PV): Defines a persistent volume for MySQL data storage. In this example, it uses a hostPath for simplicity, but in a production environment, you would use a more suitable storage backend.

PersistentVolumeClaim (PVC): Claims the persistent volume created above. This allows the StatefulSet to use the volume for storing MySQL data.

StatefulSet: Deploys a single MySQL instance, using the PVC for data persistence. It exposes port 3306 for database access.

Headless Service: The MySQL service is defined as a headless service (with clusterIP: None) to allow direct access to the MySQL pod by its hostname.

Deployment: Deploys a simple web application (using the Nginx image) with three replicas.

LoadBalancer Service: Exposes the web application to the outside world via a LoadBalancer service.

Commonly used Kubectl commands for Statefulsets

```bash
# Used for creating a StatefulSet from a YAML file
kubectl apply -f <statefulset-file>.yaml

# Used for listing all StatefulSets in the current namespace
kubectl get statefulsets

# Used for getting detailed information about a specific StatefulSet
kubectl describe statefulset <statefulset-name>

# Used for listing all pods associated with a specific StatefulSet
kubectl get pods -l app=<statefulset-label>

# Used for scaling a StatefulSet to a desired number of replicas
kubectl scale statefulset <statefulset-name> --replicas=<number>

# Used for updating the StatefulSet configuration (e.g., image version)
kubectl apply -f <updated-statefulset-file>.yaml

# Used for deleting a specific StatefulSet
kubectl delete statefulset <statefulset-name>

# Used for viewing events related to a specific StatefulSet
kubectl get events --field-selector involvedObject.kind=StatefulSet,involvedObject.name=<statefulset-name>

# Used for checking the status of a specific StatefulSet
kubectl get statefulset <statefulset-name> -o jsonpath='{.status}'

# Used for rolling back to the previous revision of a StatefulSet
kubectl rollout undo statefulset <statefulset-name>

```
