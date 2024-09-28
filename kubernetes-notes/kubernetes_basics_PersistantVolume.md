**PERSISTANT VOLUME(PV) and PERSISTANT VOLUME CLAIM (PVC)**

A **Persistent Volume (PV)** in Kubernetes is a piece of storage in a cluster that has been provisioned by an administrator or dynamically using a StorageClass. 
It is independent of the lifecycle of a pod, meaning that even if the pod is deleted, the data in the persistent volume will persist.
The PV can be backed by different storage types like NFS, iSCSI, cloud providers (AWS, GCE, etc.), or local disks.

**Persistent Volume Claim (PVC)**: Pods use PVCs to request specific storage resources from available PVs. A PVC specifies the size and access modes needed (e.g., ReadWriteOnce, ReadOnlyMany).

**Lifecycle**: PVs exist beyond the lifecycle of individual pods. This allows data to be preserved even if pods are removed or restarted.

![image](https://github.com/user-attachments/assets/1f19ecca-20e0-46ab-997f-745a14276eb2)

In Kubernetes, Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) work together to provide storage for pods that persists beyond the lifecycle of individual pods. 
On looking at the step-by-step process of how PV and PVC interact:

**1. Persistent Volume (PV)**:
A PV is a cluster resource, much like CPU or memory, that provides storage.
It is created either by a cluster administrator or dynamically provisioned through a StorageClass.
PVs are backed by physical storage, such as local disk, NFS, or cloud-based storage systems like AWS EBS or GCE PD.
The PV is an abstraction that decouples storage from the pods, ensuring that the storage exists independently of the pod lifecycle.

**2. Persistent Volume Claim (PVC)**:
A PVC is a request for storage by a pod.
It defines the size, access mode (e.g., ReadWriteOnce, ReadOnlyMany), and sometimes the desired storage class.
When a pod needs persistent storage, it makes a claim by creating a PVC.

**3. Binding**:
Kubernetes attempts to match the PVC request with an available PV that satisfies the PVC’s requirements (e.g., size, access mode).
If a suitable PV exists, the claim (PVC) is bound to the PV.
If no matching PV exists, the PVC remains unbound until a suitable PV becomes available or is dynamically provisioned.

**4. Using the Persistent Volume**:
Once the PVC is bound to a PV, the pod can access the storage by mounting the PVC to its file system.
The pod uses the storage in the PV as if it were a local disk, enabling the pod to read and write data to the PV.

**5. Lifecycle**:
The lifecycle of the pod and PVC is independent of the PV.
When a pod is deleted, the PVC still exists, and the data remains in the PV.
Even if the PVC is deleted, the data might still be available depending on the reclaim policy set for the PV (options are Retain, Recycle, or Delete).

**6. Reclaim Policy**:
Retain: The PV remains even after the PVC is deleted, and the administrator can manually recover the data.
Recycle: The PV’s data is scrubbed (deleted) and it becomes available for future use.
Delete: The PV and its underlying storage are automatically deleted when the PVC is deleted.


**Creating PV**

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

**Creating a PVC**

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

**Pod using the PVC**

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: my-storage
  volumes:
    - name: my-storage
      persistentVolumeClaim:
        claimName: my-pvc
```

---

<details>
  <summary>Real Life example - PV and PVC</summary>

Let’s use a Library System as an example to explain the relationship between Persistent Volumes (PV), Persistent Volume Claims (PVC), and Storage Classes in Kubernetes.

**Library Owner:** Manages the library and ensures it has enough books (storage) to meet the needs of the readers (applications).

**Bookshelves (Storage Units):** The library contains bookshelves, which hold different categories of books. Each bookshelf has a certain capacity. 
These bookshelves are like Persistent Volumes (PVs) — the actual physical storage units that hold the data (books).

**Readers (Consumers):** Library readers represent the applications (pods) that need access to books. 
Readers go to the librarian (Kubernetes) and request specific books, which is like a Persistent Volume Claim (PVC) — asking for specific storage resources (in this case, books).

**Bookshelf Types (Storage Class):** The library can have different types of bookshelves based on size, location, or special requirements (e.g., rare book section, general reading section). 
These represent Storage Classes, which define the type of storage (e.g., SSD for high performance, or HDD for standard use).

**Mapping to Kubernetes Concepts:**

- Library Owner (Kubernetes Admin):
The library owner is like the Kubernetes administrator, responsible for setting up and organizing storage (bookshelves) and ensuring there are enough resources for readers (pods).

- Bookshelves (Persistent Volumes - PVs):
The bookshelves represent Persistent Volumes in Kubernetes. Each bookshelf has a defined capacity (e.g., 100 books) and can serve specific categories (fiction, reference books, etc.).
Example: A bookshelf with a 100-book capacity is a PV that has 100Gi of storage.

- Readers (Persistent Volume Claims - PVCs):
The readers in the library represent pods. When readers need specific books (storage), they make a claim to the librarian for a certain number of books or space.
If the bookshelf (PV) has enough space, the librarian assigns it to the reader.
Example: A reader requests 20 books, which is a PVC asking for 20Gi of storage.

- Bookshelf Types (Storage Class):
Different bookshelves in the library serve different purposes, like high-value books stored in a secure section (premium storage) versus general reading books (standard storage). This is akin to Storage Classes in Kubernetes.
Example: A premium bookshelf may be climate-controlled and locked, while a regular bookshelf may be available for any reader to access freely.

</details>

---

When creating PV and PVC in Kubernetes below recommended best practices will ensure efficient storage utilization, security, performance, and maintainability. 

**1. Choose Appropriate Storage Type**
- _Match PV type to workload_: Use different storage backends depending on the workload's requirements (e.g., high-performance SSD for databases, networked storage like NFS for shared data).
- _Leverage Cloud Storage_: For cloud deployments, use cloud-native storage options like AWS EBS, GCP Persistent Disks, or Azure Disks.
- _Avoid local storage for critical data_: Local storage is node-bound, so it may lead to data loss if the node fails.

**2. Use StorageClasses for Dynamic Provisioning**

- _Dynamic provisioning_: Set up StorageClasses to dynamically provision PVs as needed by PVCs. This ensures you don’t need to manually create PVs for every PVC.
- _Use multiple StorageClasses_: Create different StorageClasses for different storage types (e.g., SSD, HDD, NFS) or different access modes (e.g., ReadWriteOnce, ReadWriteMany).
- _Set default StorageClass_: Make sure there’s a default StorageClass for general-purpose storage requests, which helps in simplifying PVC definitions.

**3. Use Proper Reclaim Policy**
- _Set reclaim policy appropriately_:
  - Retain: Use for data that must be manually reclaimed (e.g., databases).
  - Delete: Use for ephemeral or disposable data (e.g., logs, caches) to automatically clean up storage when the PVC is deleted.
- _Avoid unintended data loss_: Be cautious when using Delete as it can remove data if the PVC is deleted.

**4. Correct Access Modes**
- _Select the correct access mode_:
  - ReadWriteOnce (RWO): Single node can read/write the volume (ideal for databases, stateful workloads).
  - ReadOnlyMany (ROX): Multiple nodes can read from the volume (useful for shared data).
  - ReadWriteMany (RWX): Multiple nodes can read/write (suitable for shared workloads like logs or shared file systems).
- Use RWX cautiously since shared access can impact performance and consistency.
  
**5. Specify Resource Requests and Limits**
- _Size appropriately_: Ensure the PVC requests the correct size of storage needed. Too small may lead to application failures; too large may waste resources.
- _Set growth strategy_: If your workload can grow, consider using storage backends that support resizing volumes, and make sure your underlying infrastructure supports volume expansion.

**6. Security Considerations**
- _Access control_: Use RBAC (Role-Based Access Control) to restrict which users or services can create, modify, or delete PVs and PVCs.
- _Data encryption_: Ensure data-at-rest encryption is enabled for cloud volumes (e.g., AWS EBS encryption, Azure Disk encryption).
- _Isolate sensitive data_: Store sensitive data in dedicated, secure storage backends (e.g., isolated disks for databases).

**7. Backup and Disaster Recovery**
- _Backup PV data_: Implement regular backups for PVs storing critical data (e.g., use cloud-native backup solutions or Kubernetes backup tools like Velero).
- _Snapshots_: Use storage backends that support snapshot capabilities for quick recovery in case of failure.
- _Test restoration_: Regularly test backups to ensure you can restore data from them.

**8. Monitor and Manage Storage Utilization**
- _Use metrics and alerts_: Monitor PVC and PV usage with Kubernetes monitoring tools (e.g., Prometheus) to track storage consumption, performance, and errors.
- _Quota management_: Use resource quotas to control the amount of storage requested by users or namespaces to prevent resource exhaustion.

**9. Label and Annotate PVs and PVCs**
- _Organize resources_: Apply labels and annotations to PVs and PVCs for easy identification, management, and filtering.
- _Track ownership_: Use labels to track which application, team, or namespace owns a particular PV or PVC.

**10. Plan for Storage Expansion**
- _Use expandable storage_: Make sure your PVs support expansion if you anticipate your storage needs growing.
- _Enable PVC resizing_: Kubernetes supports dynamic resizing of PVCs, so ensure that your StorageClass and storage provider allow this functionality.

**11. Use Volume Snapshots for Stateful Applications**
- For stateful applications like databases, use Volume Snapshots to take point-in-time backups of PVs.
- Ensure your storage backend supports volume snapshots and that your recovery process is tested.

**12. Cleanup and Recycling**
- _Proper cleanup_: Ensure PVs are properly recycled or deleted when no longer needed. Use lifecycle hooks to automate cleanup (e.g., with reclaim policies).
- _Manual reclaim if needed_: If using Retain reclaim policy, ensure that unused PVs are manually cleaned to avoid resource wastage.


Considering the above recommended best practices and security features, we can create a single yaml file for PV and PVC

```yml

# Persistent Volume definition

apiVersion: v1
kind: PersistentVolume
metadata:
  name: secure-pv
  labels:
    app: secure-app
    env: production
    team: devops
  annotations:
    purpose: "Used for storing critical production data"
spec:
  capacity:
    storage: 20Gi                         # Sizing the PV appropriately
  accessModes:
    - ReadWriteOnce                        # Appropriate access mode for single node write
  persistentVolumeReclaimPolicy: Retain    # Data will be retained even if PVC is deleted
  storageClassName: fast-storage           # Specifying the StorageClass for dynamic provisioning
  nfs:                                     # Example of using NFS as storage backend
    path: /srv/nfs/kubedata
    server: nfs.example.com
  mountOptions:
    - hard                                 # NFS mount option for resilience
    - nfsvers=4.1                          # NFS version for compatibility
  nodeAffinity:                            # PV affinity to nodes (security and control over placement)
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/os
              operator: In
              values:
                - linux

---

# Persistent Volume Claim definition

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: secure-pvc
  labels:
    app: secure-app
    env: production
    team: devops
  annotations:
    backup-strategy: "daily"               # Annotation for backup strategy (part of DR plan)
spec:
  accessModes:
    - ReadWriteOnce                        # Match access mode with the PV
  resources:
    requests:
      storage: 20Gi                        # Requesting the same size as the PV
  storageClassName: fast-storage           # Specifying the storage class
  volumeMode: Filesystem                   # Volume mode set to file system
  dataSource:                              # Enable snapshot-based restore if supported
    kind: VolumeSnapshot
    name: prod-snapshot
  selector:                                # Binding PVC to specific PV with matching labels
    matchLabels:
      app: secure-app
  securityContext:                         # Adding security context (for Pod security)
    fsGroup: 2000                          # File system group for ownership control
    runAsUser: 1000                        # User ID for accessing storage
    seLinuxOptions:
      level: "s0:c123,c456"                # SELinux security context

---

# StorageClass definition (if dynamic provisioning is used)

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"  # Default StorageClass for cluster
provisioner: kubernetes.io/aws-ebs                        # Storage backend (example for AWS EBS)
parameters:
  type: gp2                                                # SSD-based storage
  encrypted: "true"                                        # Enable encryption at rest
allowVolumeExpansion: true                                 # Enable volume expansion
mountOptions:
  - debug                                                  # Enable mount-level debugging
reclaimPolicy: Retain                                       # Retain data after PVC deletion
volumeBindingMode: Immediate                                # Volume binding mode
```

