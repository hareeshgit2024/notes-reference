**INTRODUCTION**

Docker is an open source platform that enables developers to package applications and their dependencies into portable containers, ensuring consistency across different environments. This helps the developer to make sure whatever features tested in development phase and signedoff will work exactly as expected in the production environments. It simplifies deployment, scaling, and management of applications. On How part, we will definitely cover in the below.

**Virtual Machine vs Docker**

Virtual Machines works as a virtual computer system with its own CPU, memory, interface network and storage created on a physical system hosted on premise or remote. Software called hypervisor separates the machine from the underlying hardware. VM's have applications, associated libraries and a guest operating system which makes it heavy. Each VM's are isolated from each other.
On the other side, Docker engines sit in between the host OS and applications. Here there is no guest OS and applications can directly talk with host OS. As there is no guest OS bundled, it is light weight.

![image](https://github.com/user-attachments/assets/d502281b-c612-4104-a78c-7f7a362676e1)

Virtual Machines (VMs) virtualize entire hardware systems, running a full operating system with dedicated resources, which can be resource intensive. Docker containers, on the other hand, share the host OS kernel, making them more lightweight and efficient. VMs offer strong isolation at the cost of overhead, while Docker containers are faster to start and consume fewer resources. VMs are ideal for running different OS environments, whereas Docker excels in microservices and rapid deployment scenarios.

<details>
.
<summary>
  Docker, VM explained - real life example
</summary>

Imagine you're setting up a new coffee shop. Instead of building the shop from scratch every time, you use a portable, fully-equipped coffee cart that has everything you need—coffee beans, machines, cups, and even a small menu. You can take this cart anywhere, and it works the same way whether you're in a park, at a festival, or on the street corner. This coffee cart is like a Docker container.

In software development, Docker containers are like these portable coffee carts. They package everything an application needs—code, libraries, and settings—so it can run anywhere, just like your coffee cart can serve coffee anywhere. Whether you're running your container on a developer's laptop, a testing environment, or a cloud server, it will work the same way.

This is different from setting up a full coffee shop (like a virtual machine), which requires more time, resources, and space. Containers are lightweight, quick to start, and can be easily moved or replicated, much like a portable cart. This portability and consistency make Docker a powerful tool for developers, ensuring their applications work seamlessly across different environments.
</details>

**Docker Architecture - an overview** 

Docker's architecture is based on a client-server model, where the Docker client communicates with the Docker daemon [dockerd], which builds, runs, and manages containers. Containers are created from images, which are lightweight, standalone, and executable software packages. Docker uses a _layered filesystem_, where images are composed of layers that represent different stages of the application's build process. Docker Hub or private registries store and distribute these images. The architecture allows for efficient, scalable, and portable deployment of applications across different environments.
![image](https://github.com/user-attachments/assets/a2841777-6b82-4767-8656-86d60b190bd6)

Docker Engine is an open source containerization technology for building and containerizing your applications. Docker Engine acts as a client-server application with:

- A server with a long-running daemon process `dockerd`.
- APIs which specify interfaces that programs can use to talk to and instruct the Docker daemon.
- A command line interface (CLI) client docker.

<details>
.
<summary>Docker Image, Container, Registry, Engine - real life example</summary>

Imagine you're running a food truck business:

Docker Image: This is like your secret recipe and blueprint for a specific dish. It includes everything needed to make the dish—ingredients (code), cooking instructions (dependencies), and presentation guidelines (settings). Each image is a ready-to-use template for a meal.

Docker Container: This is the actual dish prepared using the recipe. When you want to serve food, you use the image (recipe) to create a container (dish). Each container is a running instance of the image, just like each dish is a prepared meal ready to serve to customers.

Docker Engine: This is the kitchen in your food truck. It's the engine that takes the image (recipe) and turns it into a container (dish). The Docker Engine handles all the cooking processes, ensuring that each dish is prepared correctly and efficiently, regardless of where your food truck is parked.

Docker Registry: This is your recipe book or storage cabinet. It stores all your images (recipes) so you can pull them out and use them whenever you need to prepare a dish. Public registries (like Docker Hub) are like community cookbooks, where everyone can share and access recipes.
</details>

**Docker Installation**

Begin by installing Docker [Reference : https://docs.docker.com/get-docker/] on your machine using Docker Desktop for Windows/macOS or Docker Engine for Linux. After installation execute the below commands to confirm the docker installation in our machine,

```Dockerfile syntax
docker version
```
```Dockerfile syntax
docker-compose version
```

**Docker Internals**

![image](https://github.com/user-attachments/assets/3a6cb3ec-4e11-4ab3-98e5-4d2bd47a6855)

Docker containers rely heavily on the Linux kernel for process isolation, resource management, and security. This close relationship allows Docker to be lightweight and efficient, making it an ideal tool for modern application development and deployment. We can go through various componenets within the Linux

Linux Kernel and Containers: The Linux kernel is the core of a Linux operating system, managing system resources, hardware communication, and process isolation. Docker containers share the host machine's Linux kernel. This is different from virtual machines, which run their own guest operating systems on top of a hypervisor. Docker containers are lightweight because they don't need to include a full OS—they rely on the host's kernel.

Namespaces: Namespaces are a feature of the Linux kernel that allow for the isolation of processes, network interfaces, and other resources. Each Docker container operates within its own set of namespaces, making it appear as if it's running on a completely separate system. Types of Namespaces: Docker uses various types of namespaces like PID (Process ID), NET (network), MNT (mount), IPC (inter-process communication), and UTS (hostname) to isolate containers from each other and from the host.

Control Groups (cgroups): Cgroups are another Linux kernel feature used by Docker to limit, prioritize, and account for the resource usage (CPU, memory, disk I/O, etc.) of containers. This ensures that one container cannot consume all of the host's resources.

Union File Systems: Docker uses union file systems (like AUFS, OverlayFS) to manage container layers. These filesystems allow multiple layers to be stacked, with each layer representing changes from the previous one. This makes container images lightweight and allows for efficient storage and distribution. A container's writable layer is tightly coupled to the host machine where the container is running. You can't easily move the data somewhere else. Writing into a container's writable layer requires a storage driver to manage the filesystem.

Isolation and Security: The combination of namespaces and cgroups provides a level of isolation between containers, which enhances security. Additionally, Linux Security Modules (LSM) like AppArmor or SELinux can be used to further secure containers by restricting what a container can do on the host system.

**Playing with Docker Images** 

Containers allow developers to package software to run on any target system. Containerization allows a software application to run as microservices across distributed, cross-platform hardware architectures. Because containers are highly portable, these software applications run on almost any machine with speedy deployment. For example, an enterprise application may have hundreds of microservices. They could run as containers across multiple machines and virtual machines (VMs) in a dedicated data center and the cloud.

How Docker images work
  
A Docker image, or container image, is a standalone, executable file used to create a container. This container image contains all the libraries, dependencies, and files that the container needs to run. A Docker image is shareable and portable, so you can deploy the same image in multiple locations at once—much like a software binary file. 

You can store images in registries to keep track of complex software architectures, projects, business segments, and user group access. 

>[!NOTE]
>Make sure you logged in into docker hub or any docker registry.
>Docker daemon / Docker desktop is running

- **Build an Image**: Create a custom image using a Dockerfile and the `docker build -t <tag-name> .` command. It will automatically pick the default named Dockerfile, else need to specify using '-f'.
- **List Images**: Display a list of all available images on your system with `docker images` or `docker image ls` or `docker image ls -a`.
- **Inspect an Image**: Get detailed information about an image with `docker inspect <image-id>`. image-id can be obtained from previous step.
- **Remove an Image**: Delete an image from your system using `docker rmi <image-id>` or `docker image rm <image-id>` or `docker image rm <image-id> -f`.
- **Run a Container from an Image**: Use `docker run <image-id>` to start a container from an image.
- **Push an Image**: Upload an image to a Docker registry with `docker push <image-tag>`, such as `docker push myrepo/myapp:v1.0`.
- **Check Image Size**: View the size of an image with `docker images --format "{{.Size}}"`.
- **Rename an Image tag**: Assign a new tag to an image with `docker tag <image-id> <new-tag>`. For example, `docker tag myapp:latest myrepo/myapp:v1.0`.
- **Search for Images**: Use `docker search <image-name>` to find images available in Docker Hub.
- **Pull an Image**: Retrieve an image from a registry using `docker pull <image-name>:<tag>`. For example, `docker pull ubuntu:latest` pulls the latest Ubuntu image.
- **List Image Tags**: View all tags for a particular image using `docker images --filter=reference=<image-name>`.
- **Save an Image**: Export an image to a tarball file using `docker save -o <file-name>.tar <image-id>`.
- **Load an Image**: Import an image from a tarball file with `docker load -i <file-name>.tar`.
- **Build with Cache**: Use `docker build --no-cache -t <tag-name> .` to build an image without cache, ensuring all steps are executed afresh.
- **View Image History**: Examine the layers and changes of an image with `docker history <image-id>`.
- **Create a Multi-Stage Build**: Use multi-stage builds in your Dockerfile to optimize image size by separating the build environment from the runtime environment.
- **Commit Changes**: Save changes from a running container to a new image using `docker commit <container-id> <new-image-tag>`.

**Playing with Docker Containers**

Docker is a containerization platform that you can use to package software in containers and run them on target machines. Docker containers run on any machine or virtual machine where the Docker engine is installed. The Docker engine only runs on the Linux operating system.

How Docker containers work
A Docker container is a runtime environment with all the necessary components—like code, dependencies, and libraries—needed to run the application code without using host machine dependencies. This container runtime runs on the engine on a server, machine, or cloud instance. The engine runs multiple containers depending on the underlying resources available. 

>[!NOTE]
>Make sure you logged in into docker hub or any docker registry.
>Docker daemon / Docker desktop is running

- **Run the container:** Use the command `docker run -it nginix:latest` to run the nginix image, which is available with the 'latest' tag
- **List running containers:** Use the command `docker ps` or `docker container ls` or `docker container ls -a` to list all running containers
- **Stop a running container:** Use the command `docker stop <container_id>` to stop a running container. Container_id can be obtained from the above command
- **Start a container:** Use the command `docker start <container_id>` to start a running container. Container_id can be obtained from the above command
- **Restart a container:** Use the command `docker restart <container_id>` to restart a running container. Container_id can be obtained from the above command
- **Remove a container:** Use the command `docker rm <container_id>` to remove a stopped container. `docker rm <container_id> -f` can be used to forcefully remove the container
- **Kill a container:** Use the command `docker kill <container_id>` to kill a running container.
- **Inspect a container:** Use the command `docker inspect <container_id>` to get detailed information on a container
- **Remove all stopped container:** Use the command `docker container prune`
- **Fetch the logs of a container:** Use the command `docker logs <container_id>` to get logs of a container
- **Get resource use of container:** Use the command `docker stats` to display a live stream of container(s) resource usage statistics
- **Pause a running container:** Command `docker pause <container_id>` will pause all processes within a container
- **UnPause a running container:** Command `docker unpause <container_id>` will unpause all processes within a container
- **Rename a container:** Command `docker rename <old_name> <new_name>` will rename the container to a new_name
- **Run command inside a running container:** Command `docker exec -it <container_id> bash` will run a command inside a running container

**Dockerfile - Overview**

Dockerfile, will be a text document (without file extension) in which we define the container build instruction in an ordered way. We write the Dockerfile in a domain-specific language, called the Dockerfile syntax.

Sample Dockerfile is as below:

```Dockerfile syntax

FROM openjdk:22-jdk-alpine
WORKDIR /app
COPY target/myapp.jar /app/myapp.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "myapp.jar"]
```
<details>
.
<summary>Step by Step explanation of the above dockerfile</summary>
  
**FROM openjdk:22-jdk-alpine**
Purpose: Specifies the base image for your Docker image. Here, we're using the OpenJDK 22 image with Alpine Linux, which is a lightweight Linux     distribution.
Why: Alpine is chosen for its minimal size and fast performance, and OpenJDK 22 is used to ensure you're running the latest Java version.

**WORKDIR /app**
Purpose: Sets the working directory for the subsequent instructions in the Dockerfile. This directory will be created if it doesn't exist.
Why: Using a dedicated working directory helps keep your container organized and simplifies file paths for commands.

**COPY target/myapp.jar /app/myapp.jar**
Purpose: Copies the JAR file from your host machine (under the target directory) to the working directory inside the container.
Why: This step is necessary to include your application code in the Docker image.

**EXPOSE 8080**
Purpose: Documents the port on which the application will listen inside the container. It doesn’t actually publish the port but serves as a hint to users of the image.
Why: Helps with network configurations, especially in orchestration environments like Kubernetes.

**ENTRYPOINT ["java", "-jar", "myapp.jar"]**
Purpose: Specifies the command to run when the container starts. Here, it runs your Java application.
Why: Setting the ENTRYPOINT makes the container behave as an executable with the specified command.
</details>

**What does 'layered filesystem' means.**? : Docker build usually consists of a series of ordered build instructions written in a file called _Dockerfile_. Each instruction in the dockerfile gets translated into a layer of docker image. The below image gives a high level overview on the layered structure
![image](https://github.com/user-attachments/assets/fbca45e1-02d2-4553-ab99-03cbaf42819d)
When we rerun the build using the same dockerfile after perfoming some updates in the instruction,  If a layer of an image is unchanged, then the builder picks it up from the _build cache_. If a layer has changed since the last build, that layer, and all layers that follow, must be rebuilt.

**Multi-stage build:**

A multistage Docker build allows you to optimize your Docker images by using multiple `FROM` instructions to create intermediate images. 

- Multistage builds use multiple FROM instructions to create a series of images, where each stage can have its own base image and dependencies.
- You can copy artifacts from one stage to another, ensuring that the final image contains only the necessary files and is as small as possible.
- This technique helps reduce the final image size and improve build efficiency by isolating build dependencies from runtime dependencies.

_Example_:

- Stage 1: Build
```Dockerfile syntax
FROM maven:3.8.6-openjdk-22 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package
```
- Stage 2: Runtime
```Dockerfile syntax
FROM openjdk:22-jdk-alpine
WORKDIR /app
COPY --from=builder /app/target/myapp.jar /app/myapp.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "myapp.jar"]
```
<details>
  .
<summary>How multi stage works</summary>
  
`Build Stage (maven:3.8.6-openjdk-22):`

FROM maven:3.8.6-openjdk-22 AS builder: Uses the Maven image with OpenJDK 22 for the build stage and names this stage builder.
WORKDIR /app: Sets the working directory.
COPY pom.xml .: Copies the Maven project descriptor file.
COPY src ./src: Copies the source code.
RUN mvn clean package: Builds the application and packages it into a JAR file.

`Runtime Stage (openjdk:22-jdk-alpine):`

FROM openjdk:22-jdk-alpine: Uses a lightweight OpenJDK 22 image for the final runtime environment.
WORKDIR /app: Sets the working directory.
COPY --from=builder /app/target/myapp.jar /app/myapp.jar: Copies the built JAR file from the build stage to the runtime stage.
EXPOSE 8080: Exposes the application port.
ENTRYPOINT ["java", "-jar", "myapp.jar"]: Defines the command to run the application.
</details>

As we got a high level overview of Docker, Image, Container lets gets some handon.

**Containerizing with Docker**

To revisit the basic steps involved in containerising a docker image will include the below steps.

- **Write a Dockerfile**: Create a Dockerfile to define your application's environment, specifying the base image, dependencies, and build instructions.
- **Build an Image**: Use the docker build command to generate a Docker image from your Dockerfile, encapsulating your application and its dependencies.
- **Run a Container**: Start a container from the image using docker run, specifying options like port mappings and environment variables.
- **Manage Containers**: Use commands like docker ps to list running containers and docker start or docker stop to start/stop them as needed.
- **Optimize Images**: Regularly update your Dockerfile and images to reduce size and improve performance.
- **Use Docker Compose**: For multi-container applications, create a docker-compose.yml file to define and manage multi-container setups.
- **Push to Registry**: Share your images by pushing them to Docker Hub or a private registry, enabling easy deployment across different environments.

**Manage data in Docker**

All files created inside a container are stored on a writable container layer. Since the storage is within the container, when the container exit, the data is lost and will be difficult to get that data if in case any other container needs it.

Docker has two options for containers to store files on the host machine, so that the files are persisted even after the container stops: `volumes`, and `bind mounts`.
Docker also supports containers storing files `in-memory on the host machine`. Such files are not persisted. If you're running Docker on Linux, `tmpfs mount` is used to store files in the host's system memory. If you're running Docker on Windows, `named pipe` is used to store files in the host's system memory.

**Volumes**

Volumes are created and managed by Docker. You can create a volume explicitly using the `docker volume create` command. When you create a volume, it's stored within a directory on the Docker host. When you mount the volume into a container, this directory is what's mounted into the container. A given volume can be mounted into multiple containers simultaneously.

**Bind mounts**

When you use a bind mount, a file or directory on the host machine is mounted into a container. The file or directory is referenced by its full path on the host machine. The file or directory doesn't need to exist on the Docker host already. It is created on demand if it doesn't yet exist. Bind mounts are fast, but they rely on the host machine's filesystem having a specific directory structure available. You can't use Docker CLI commands to directly manage bind mounts.

**tmpfs**

A tmpfs mount isn't persisted on disk, either on the Docker host or within a container. It can be used by a container during the lifetime of the container, to store non-persistent state or sensitive information.

**Named pipes**

Named pipes can be used for communication between the Docker host and a container. Common use case is to run a third-party tool inside of a container and connect to the Docker Engine API using a named pipe.

<details>
  <summary>Volumes, Bind mounts, tmpfs, Named pipes - real life example</summary>
.
Think of running a bakery where you need to store ingredients, tools, and temporary items:

Docker Volumes: These are like dedicated storage rooms in your bakery. You keep your essential ingredients (data) here—flour, sugar, etc. (database files, logs). The volume is separate from your kitchen (container) but always accessible, ensuring that even if you remodel your kitchen (rebuild the container), your ingredients (data) are safe and available.

Bind Mounts: Imagine a shared pantry between your bakery and your home. You can access the same ingredients (files) from both places. Bind mounts link a specific location in your bakery (container) to a specific spot in your house (host system). This is useful when you want to directly edit ingredients (files) from outside the bakery (container) while the bakery is still running.

tmpfs: This is like a temporary counter space in your bakery. You use it to quickly prepare or store items that you don’t need to keep for long—like mixing dough before baking (temporary files). Everything on this counter is cleared when you’re done for the day (when the container stops).

Named Pipe: This is like a communication tube between two chefs in different parts of the bakery. It allows them to pass notes (data) back and forth quickly and directly without using the main kitchen pathways (filesystem). It's useful for sending messages (inter-process communication) between different areas (containers).
</details>

**Docker Volume Advanced Concepts**

Docker volumes are an essential component of Docker's storage management, enabling persistent data storage across container lifecycles. Understanding advanced concepts and commands associated with Docker volumes is crucial for managing complex containerized applications.

1. **Volume Persistence and Lifecycle**
Persistence: Unlike bind mounts, volumes are completely managed by Docker and are independent of the host file system. This means data stored in volumes remains accessible even after the container is stopped or removed.
Volume Lifecycle: Volumes can outlive containers, meaning they persist even if the container that created them is deleted. This makes them ideal for use cases where data needs to be retained across container restarts or migrations.

2. **Volume Types**
Anonymous Volumes: Created automatically by Docker when you don't specify a volume name. These volumes are temporary and can be difficult to manage if not explicitly named.
Named Volumes: These are user-defined and named explicitly, making them easier to manage and reuse across containers.
External Volumes: Managed outside of Docker, such as cloud-based storage solutions (e.g., AWS EBS). These volumes are useful for data that needs to be accessed by containers running on different Docker hosts.

3. **Volume Sharing and Mounting Options**
Read-Only and Read-Write Mounts: When mounting a volume, you can specify whether the container should have read-write (rw) or read-only (ro) access. This is useful for protecting data integrity in scenarios where multiple containers share the same volume.
Propagation Flags: These control how mount points are shared between the host and the container. For instance, shared, slave, and private are propagation modes that determine the visibility and accessibility of mount points across different namespaces.

4. **Volume Drivers**
Docker allows the use of volume drivers to manage volumes. These drivers enable integration with external storage systems, allowing for advanced features like automated backups, replication, and encryption. For example, using a volume driver like rexray/ebs allows Docker to directly manage AWS EBS volumes.

5. **Multi-Host Volumes**
In a swarm cluster, volumes can be shared between nodes using NFS (Network File System) or other distributed file systems like GlusterFS. This allows containers running on different hosts to access the same data, facilitating stateful applications in a distributed environment.

**Playing with Docker Volumes**

Docker volumes are versatile and essential for managing data in containerized environments. By mastering advanced concepts and the associated Docker commands, you can ensure data persistence, optimize storage management, and improve the robustness of your Dockerized applications.

- **Creating a Volume :** `docker volume create my_volume` Creates a named volume called **my_volume**.
- **Listing Volumes :** `docker volume ls` Lists all volumes managed by Docker.
- **Inspecting a Volume :** `docker volume inspect my_volume` Displays detailed information about the specified volume, including its mount point on the host, driver, and labels.
- **Removing a Volume :** `docker volume rm my_volume` Removes the specified volume. Note that you cannot remove a volume that is currently in use by a container.
- **Pruning Unused Volumes :** `docker volume prune` Deletes all unused volumes, freeing up storage space.
- **Mounting a Volume to a Container :** `docker run -d --name my_container -v my_volume:/app/data my_image` Runs a container with the named volume my_volume mounted at /app/data inside the container.
- **Read-Only Volume Mount :** `docker run -d --name my_container -v my_volume:/app/data:ro my_image` Mounts the volume as read-only, preventing the container from modifying the data.
- **Using Bind Mounts with Volumes :** `docker run -d --name my_container -v /host/data:/app/data my_image` Mounts a specific directory from the host (/host/data) into the container. This is different from volumes but often used in combination for complex setups.
- **Specifying a Volume Driver :** `docker volume create --driver rexray/ebs --opt size=20 my_volume` Creates a volume with a specific driver, in this case, using rexray/ebs for managing an EBS volume on AWS.
- **Using a Volume with Docker Compose :**
```Dockerfile syntax
version: '3'
services:
  app:
    image: my_image
    volumes:
      - my_volume:/app/data
volumes:
  my_volume:
    external: true
  ```
In Docker Compose, volumes can be declared and attached to services, with support for external volumes managed outside of Docker.

**Docker Network**

Docker networks are essential for enabling communication between containers, the host system, and external networks. Docker provides several types of networks to suit different use cases:

1. **Bridge Network**
Description: The default network type created when Docker is installed. It’s an internal, isolated network where containers on the same bridge can communicate with each other using container names, but they are isolated from external networks unless explicitly configured.
Use Case: Ideal for scenarios where containers need to communicate within the same host but remain isolated from the outside world.
2. **Host Network**
Description: In a host network, the container shares the host machine’s network stack, meaning it uses the host’s IP address and network interfaces. This reduces network overhead but also eliminates isolation between the container and the host.
Use Case: Useful when performance is critical, and network latency needs to be minimized, such as in high-speed data processing.
3. **Overlay Network**
Description: An overlay network is used to connect containers across multiple Docker hosts, often in a Docker Swarm cluster. It creates a virtual network that allows containers on different hosts to communicate securely as if they were on the same local network.
Use Case: Perfect for distributed applications that require communication between containers on different physical or virtual machines.
4. **Macvlan Network**
Description: This network assigns a unique MAC address to each container, allowing them to appear as physical devices on the network. Containers are directly exposed to the network and can communicate with external networks without any NAT (Network Address Translation).
Use Case: Suitable for legacy applications or when containers need to be directly accessible from the external network.
5.** None Network**
Description: Completely disables networking for the container, resulting in total isolation from other containers and the host.
Use Case: Used for highly secure or resource-constrained applications that do not require network access.

<details>
<summary>Docker Network - real life example</summary>
.
Imagine you live in an apartment building:

Bridge Network: This is like living in a gated community. You and your neighbors (containers) can talk to each other directly within the community (network). However, to talk to someone outside the gate (external network), you need to use the community's main gate (router). It’s safe and keeps things organized.

Host Network: This is like living in a shared, open space without walls. You and your roommates (containers) share everything, including the same address (IP). Communication is super fast because you’re all in the same space, but there’s no privacy or separation.

Overlay Network: Imagine multiple apartment buildings in different cities connected by a private, secure phone line (network). Even though you’re physically far apart, you can talk to someone in another building as if they were next door. This connection lets you work together across distances seamlessly.

Macvlan Network: This is like each apartment in your building having its own direct phone line (IP address) to the outside world. Everyone is connected directly without going through the building’s central system, allowing for more direct communication with the outside.

Each Docker network type helps containers communicate based on the level of isolation, speed, and direct access required, much like how people communicate differently based on where they live.
</details>

**Playing with Docker Network**

- **List Networks :** `docker network ls` Lists all existing Docker networks.
- **Create a Network :** `docker network create --driver bridge my_bridge_network` Creates a new network with a specified driver (e.g., bridge, overlay).
- **Inspect a Network :** `docker network inspect my_bridge_network` Displays detailed information about a specific network.
- **Connect a Container to a Network :** `docker network connect my_bridge_network my_container` Connects an existing container to a specified network.
- **Disconnect a Container from a Network :** `docker network disconnect my_bridge_network my_container` Disconnects a container from a specified network.
- **Remove a Network :** `docker network rm my_bridge_network` Deletes a specified network. Note that the network must not be in use by any containers.

