
# JENKINS

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


Sharing a sample Jenkinsfile that follows best practices for a typical CI/CD pipeline with multiple stages for Build, Development, Testing, Code Scanning (with SonarQube), and various other stages. 

The pipeline also incorporates concepts like parallel testing, failure handling, and best practices for maintainability.

```groovy

pipeline {
    agent {
        label 'master' // Specify the agent label, e.g., master or custom agent label
    }

    environment {
        CONFIG = readYaml(file: 'config.yml') // Load external config file
        SONARQUBE_URL = "http://sonarqube.yourdomain.com"
        SONARQUBE_PROJECT_KEY = "your-project-key"
        SONARQUBE_TOKEN = credentials('sonar-token') // SonarQube token stored in Jenkins credentials
    }

    options {
        timeout(time: 60, unit: 'MINUTES') // Timeout after 60 minutes
        timestamps()                       // Add timestamps to the console output
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                git url: 'https://github.com/yourusername/your-repo-name.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo "Building the project with Maven..."
                sh "mvn clean package -Dmaven.test.skip=${CONFIG.build.skipTests}"
            }
            post {
                failure {
                    echo "Build failed!"
                    emailext to: "${CONFIG.notifications.email}",
                        subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: "Build failed. Check console output at ${env.BUILD_URL}"
                }
            }
        }

        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        echo "Running unit tests..."
                        sh "mvn test -Dsuite=${CONFIG.tests.testSuites[0]}"
                    }
                }
                stage('Integration Tests') {
                    steps {
                        echo "Running integration tests..."
                        sh "mvn integration-test -Dsuite=${CONFIG.tests.testSuites[1]}"
                    }
                }
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml' // Publish test results
                }
            }
        }

        stage('Code Scanning with SonarQube') {
            steps {
                echo "Running SonarQube analysis..."
                withSonarQubeEnv('SonarQube') {
                    sh """
                    mvn sonar:sonar \
                        -Dsonar.projectKey=${SONARQUBE_PROJECT_KEY} \
                        -Dsonar.host.url=${SONARQUBE_URL} \
                        -Dsonar.login=${SONARQUBE_TOKEN}
                    """
                }
            }
            post {
                always {
                    echo "SonarQube scan completed."
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo "Checking SonarQube Quality Gate..."
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy to Development') {
            when {
                branch 'development' // Only run for development branch
            }
            steps {
                echo "Deploying to Development Environment..."
                deployToKubernetes('development') // Custom function for K8s deployment
            }
        }

        stage('Deploy to Staging') {
            when {
                branch 'staging' // Only run for staging branch
            }
            steps {
                echo "Deploying to Staging Environment..."
                deployToKubernetes('staging') // Custom function for K8s deployment
            }
        }

        stage('Deploy to Production') {
            when {
                branch 'main' // Only run for the main branch
            }
            steps {
                echo "Deploying to Production Environment..."
                deployToKubernetes('production') // Custom function for K8s deployment
            }
        }

        stage('Cleanup') {
            steps {
                echo "Cleaning up..."
                cleanWs() // Clean up workspace after build
            }
        }
    }

    post {
        always {
            echo "Pipeline completed!"
        }
        success {
            emailext to: "${CONFIG.notifications.email}",
                subject: "Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The pipeline was successful. Check the details at ${env.BUILD_URL}"
        }
        failure {
            emailext to: "${CONFIG.notifications.email}",
                subject: "Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The pipeline failed. Check the console output at ${env.BUILD_URL}"
        }
        unstable {
            emailext to: "${CONFIG.notifications.email}",
                subject: "Unstable: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The pipeline is unstable. Check the details at ${env.BUILD_URL}"
        }
    }
}

def deployToKubernetes(env) {
    echo "Deploying to Kubernetes environment: ${env}"
    sh """
    kubectl --namespace=${CONFIG.kubernetes.namespace} \
    set image deployment/${CONFIG.kubernetes.deploymentName} \
    ${CONFIG.kubernetes.deploymentName}=${CONFIG.kubernetes.containerImage}
    """
}

```

The below is the project structure of a Java application, Jenkinsfile should come under the root structure of the project.

![image](https://github.com/user-attachments/assets/bee35867-d010-4094-a329-504283004128)

---

**III. Setting up Pipeline**

---

Out of many suggested solutions, a high-level solution for creating a reusable pipeline script could be structured as follows:

**1. Use of Environment Variables**
Configure external properties through environment variables. Each team can define their unique environment variables, such as JAVA_VERSION, NAMESPACE, etc., in their pipeline configuration file or CI/CD system settings.

```yaml
pipeline {
    agent any

    environment {
        // Define environment variables for reusability
        APP_NAME = 'my-springboot-app'
        GIT_URL = 'https://github.com/your-username/your-repository.git'
        BUILD_COMMAND = './gradlew build' // Or use 'mvn clean install' for Maven projects
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository
                git branch: 'main', url: "${GIT_URL}"
            }
        }

        stage('Build') {
            steps {
                // Run the build command dynamically based on environment variable
                sh "${BUILD_COMMAND}"
            }
        }

        stage('Test') {
            steps {
                // Run tests
                sh './gradlew test'  // Adjust based on your project's test command
            }
        }

        stage('Code Scanning with SonarQube') {
            steps {
                // Placeholder for SonarQube code scanning step
                echo 'SonarQube scanning...'
                // You can add sonar-scanner commands here
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the application (this is a placeholder step)
                echo "Deploying ${APP_NAME} to the environment..."
                // You can add actual deployment commands here
            }
        }
    }

    post {
        always {
            // Clean up workspace after build
            cleanWs()
        }
    }
}

```

**_Environment Variables:_**

APP_NAME, GIT_URL, and BUILD_COMMAND are defined as environment variables and used in different stages. This makes the pipeline reusable for different projects by simply modifying the environment variables.

**_Reusability:_**

By changing the environment variables, you can reuse this same pipeline for multiple Spring Boot or other Java-based projects.

**2. External Config Files**

Keep the external configuration in YAML or JSON files, which the pipeline script can load at runtime.

The above Jenkinsfile can be converted into a model where it reads values from an external configuration file (such as config.yml) for reusability across different projects.

a. External Config

`config.yml`

```yaml
app:
  name: "my-springboot-app"
  git_url: "https://github.com/your-username/your-repository.git"
  build_command: "./gradlew build"
  test_command: "./gradlew test"
  sonar_command: "sonar-scanner"
  deploy_command: "echo Deploying to environment"
```

`Jenkinsfile Using External Config:`

You can use the readYaml function provided by the Jenkins Pipeline Utility Steps plugin to load this config.yml file in your Jenkinsfile.

```yaml

pipeline {
    agent any

    environment {
        // Loading configuration from external YAML file
        CONFIG = readYaml file: 'config.yml'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository defined in the YAML file
                git branch: 'main', url: "${CONFIG.app.git_url}"
            }
        }

        stage('Build') {
            steps {
                // Run the build command from the YAML file
                sh "${CONFIG.app.build_command}"
            }
        }

        stage('Test') {
            steps {
                // Run the test command from the YAML file
                sh "${CONFIG.app.test_command}"
            }
        }

        stage('Code Scanning with SonarQube') {
            steps {
                // Run the SonarQube command from the YAML file
                sh "${CONFIG.app.sonar_command}"
            }
        }

        stage('Deploy') {
            steps {
                // Run the deploy command from the YAML file
                sh "${CONFIG.app.deploy_command}"
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

# Make sure you have the Pipeline Utility Steps plugin installed in Jenkins to use readYaml.

```

To reuse this pipeline for different applications, simply change the values in config.yml for each project (like git_url, build_command, etc.), and the pipeline will adapt accordingly without needing to modify the Jenkinsfile itself.

