
**JENKINS**

We shall see how we can build a CI CD pipeline in Jenkins.

How to install Jenkins in your windows machine, click here to get the step -> [Click Here](https://www.jenkins.io/doc/book/installing/windows/).

---

**I . Initial Jenkins setup**

Alternative option is to use the Docker images to run. Steps to follow.

**1. Pull the docker image**

```bash
docker pull jenkins/jenkins
```
Best practice is to use a specific version while pulling a docker image from the repository.
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

![image](https://github.com/user-attachments/assets/9c7ca7f3-6037-476a-8829-95cfcc1d21b2)


If you are using docker, use the command `docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword`

If installed directly into the windows system, use `C:\Users\<YourUsername>\.jenkins\secrets\initialAdminPassword`

Once everything is ready, the dashboard should looklike

![image](https://github.com/user-attachments/assets/b6d56219-0601-4a77-915a-4f4227025388)

---

**II. Setting up Pipeline**

---
<details>

  <summary>Guidance for Github repository setup</summary>

**Step 1: Create a GitHub Repository**

1. Go to GitHub.
2. Log in to your account.
3. Click on the + icon in the top right corner and select New repository.
4. Fill in the repository name, description (optional), and choose visibility (public or private).
5. Click Create repository.
   
**Step 2: Initialize Git in Your Project**

1. Open your terminal or command prompt.
2. Navigate to your Java application directory:
   ```bash
   cd /path/to/your/java/application
  ```
3. Initialize the new git repository
  ```bash
  git init
  ```

**Step 3: Add Remote Repository**

1. Add the above git hub repository as a remote
   ```bash
   git remote add origin https://github.com/yourusername/your-repo-name.git
   ```

**Step 4: Add Files and Commit**

1. Add your project files:
   
  ```bash
  git add .
  ```

2. Commit your changes:

  ```bash
  git commit -m "Initial commit"
  ```

**Step 5: Push to GitHub**

1. Push your local repository to GitHub:

  ```bash
  git push -u origin main
  ```
(If your default branch is master, use master instead of main.)

</details>

Github repository for your project should be now available in your github

---

