
**JENKINS**

We shall see how we can build a CI CD pipeline in Jenkins.

How to install Jenkins in your windows machine, click here to get the step -> [Click Here](https://www.jenkins.io/doc/book/installing/windows/).

---

Alternative option is to use the Docker images to run. Steps to follow.

**1. Pull the docker image**

```bash
docker pull jenkins/jenkins
```

you should see multiple layers of jenkins image getting downloaded

![image](https://github.com/user-attachments/assets/9f55de0e-98cf-4b16-a154-76d861f9d15a)

```
docker images
```

should list the docker images

![image](https://github.com/user-attachments/assets/5237a80d-3c7b-403f-a9f0-a6c520d4566e)


**2. Run Jenkins in a Container**

```bash
docker run -d -p 8080:8080 -p 50000:50000 --name jenkins jenkins/jenkins
```

```
docker ps
```

should list the containers running

![image](https://github.com/user-attachments/assets/c3de894f-906f-4fc4-989f-9c2ee0b4e8c6)

The above command is used to run a Docker container for Jenkins with specific configurations.

`docker run` : This is the command to create and start a new container from a specified image.

`-d` : This flag stands for "detached mode." It runs the container in the background and returns the container ID, allowing you to continue using the terminal without being attached to the container's process.

`-p 8080:8080` : 
- This option maps port 8080 of the Docker container to port 8080 on the host machine.
- The first 8080 is the port on the host, and the second 8080 is the port inside the container.
- This is the port you will use to access the Jenkins web interface.
  For example, you can open a web browser and navigate to `http://localhost:8080` to access Jenkins.

`-p 50000:50000`:

- Similar to the previous option, this maps port 50000 of the container to port 50000 on the host machine.
- This port is used for communication between the Jenkins master and its agents (slaves). If you have Jenkins agents that need to connect to the Jenkins master, they will use this port.

`--name jenkins`:

- This option assigns a name to the container, in this case, jenkins.
- Naming the container makes it easier to manage (e.g., starting, stopping, or removing the container) because you can refer to it by name instead of the container ID.

`jenkins/jenkins`:

- This specifies the image from which to create the container.
- In this case, it's the official Jenkins image from Docker Hub (jenkins/jenkins). If no specific version is provided, it defaults to the latest version available.

**3. Accessing the Jenkins locally**

In the web browser, you can access the url `http://localhost:8080` and complete the setup.
Unlock jenkins with the initial admin password generated during the first startup.

---

