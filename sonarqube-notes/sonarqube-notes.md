![image](https://github.com/user-attachments/assets/63768661-6af0-4285-b613-19e057ba258a)

**Sonarqube**: is an open-source platform developed by SonarSource that is used for continuous inspection of code quality. It performs automatic reviews with static code analysis to detect bugs, code smells, and security vulnerabilities in your project. SonarQube supports multiple programming languages and integrates with popular CI/CD tools, making it an essential tool for maintaining high code quality standards throughout the development lifecycle.

**Key Features of SonarQube:**

- **Static Code Analysis**: Analyzes code for potential issues such as bugs, vulnerabilities, and code smells without executing the program.
- **Multi-Language Support**: Supports over 25 programming languages, including Java, JavaScript, Python, C#, and PHP.
- **Quality Gates**: Defines a set of conditions that must be met for code to be considered production-ready, providing a clear pass/fail indicator for your project.
- **Code Coverage**: Integrates with code coverage tools to assess the extent of unit test coverage.
- **Security Reports**: Detects security vulnerabilities such as SQL injection, XSS, and others, helping teams to maintain secure code.
- **Technical Debt Management**: Calculates and tracks the amount of effort needed to fix issues, giving insights into the maintainability of the project.
- **Continuous Integration (CI) Integration**: Easily integrates with CI/CD pipelines (e.g., Jenkins, GitLab CI, Azure DevOps) to ensure code quality checks are automated.

**Advantages of Using SonarQube:**

**Improves Code Quality:**

SonarQube continuously analyzes your code and provides detailed reports on bugs, vulnerabilities, and code smells. This helps developers write cleaner, more maintainable code, reducing the likelihood of defects in production.

**Enhances Security:**

By detecting security vulnerabilities early in the development process, SonarQube helps prevent potential security breaches. It identifies common security issues like SQL injection, cross-site scripting (XSS), and others, allowing developers to address them before they become a problem.

**Supports Multiple Languages:**

SonarQube’s ability to support a wide range of programming languages makes it a versatile tool for projects that use multiple languages or switch between languages over time.

**Integrates with CI/CD Pipelines:**

SonarQube integrates seamlessly with CI/CD tools, allowing for automated code quality checks during the build process. This ensures that code quality is consistently maintained throughout the development lifecycle.

**Quality Gates for Better Decision-Making:**

The quality gate feature helps enforce standards by providing clear criteria that code must meet before it can be considered production-ready. This helps teams make informed decisions about whether code is ready to be merged or released.

**Tracks Technical Debt:**

SonarQube measures technical debt, which is the estimated time required to fix issues in the code. This helps teams prioritize refactoring and maintenance tasks to improve the long-term health of the codebase.

**Developer Engagement:**

By providing detailed, actionable feedback directly within the development workflow, SonarQube encourages developers to take ownership of code quality and continuously improve their coding practices.

**Customizable Rules and Profiles:**

Teams can customize SonarQube's analysis rules and profiles to match their coding standards and project requirements, ensuring that the tool fits seamlessly into their existing development process.

**Visibility and Reporting:**

SonarQube offers dashboards and reports that give managers and stakeholders clear visibility into code quality, allowing them to track progress and identify areas that need attention.

**Architecture of SonarQube**

SonarQube's architecture is designed to analyze and measure the quality of source code across multiple projects and programming languages. It consists of several key components that work together to provide continuous code quality inspection. Understanding these components and how they interact is crucial for effectively deploying and using SonarQube.

**Core Components of SonarQube:**

1. SonarQube Server

- Main Web Application: This is the central component that provides the user interface, allowing users to view code analysis reports, manage projects, and configure settings. The web server hosts the SonarQube dashboard, where all metrics, issues, and analysis results are displayed.
- Configuration Management: The server also handles the configuration of rules, quality profiles, and quality gates. Administrators can customize rulesets for different programming languages and set up quality gates that enforce coding standards.
- APIs: SonarQube Server exposes REST APIs that allow interaction with SonarQube's functionalities programmatically, enabling integration with other tools or automation systems.

2. SonarQube Database

- Storage of Analysis Data: All the data generated by the analysis, such as issues, metrics, quality gate statuses, and historical data, is stored in a relational database. Supported databases include PostgreSQL, MySQL, and Oracle.
- Persistent Layer: This database acts as a persistent storage layer that the SonarQube Server queries to generate reports and dashboards.
  
3. SonarQube Scanner

- Code Analysis Engine: The SonarQube Scanner is a standalone tool or a plugin integrated into build tools (e.g., Maven, Gradle, Jenkins). It is responsible for analyzing the source code of a project.
- Execution Flow: When triggered, the scanner reads the source code, applies the defined rules from the SonarQube Server, and sends the results (issues, metrics, etc.) back to the SonarQube Server.
- Distributed Execution: Multiple scanners can run in parallel to analyze different projects or modules, making the system scalable and efficient.

4. SonarQube Plugins

- Language Support: Plugins are used to extend SonarQube's capabilities, including adding support for more programming languages, enhancing existing analysis rules, or integrating with other development tools.
- Third-Party Integration: Plugins can also provide integration with tools like Jira, GitHub, and LDAP for authentication and issue tracking.
- Custom Rules: Organizations can develop custom plugins to enforce specific coding standards and rules that are unique to their environment.

5. SonarQube Compute Engine

- Processing Analysis Reports: After the scanner sends the analysis results to the SonarQube Server, the Compute Engine processes these results, calculates metrics, determines quality gate statuses, and updates the database.
- Background Tasks: It handles background tasks such as synchronizing data, re-indexing, and updating measures over time.
- Efficient Resource Utilization: The Compute Engine is optimized to handle large datasets and complex calculations efficiently, ensuring that the analysis results are available in a timely manner.

6. ElasticSearch (Embedded Search Engine)

- Indexing and Searching: SonarQube uses ElasticSearch to index the analysis data and make it searchable. This allows users to quickly search for issues, metrics, and code segments across projects.
- Real-time Data Access: ElasticSearch ensures that the data is available in real-time for reporting and dashboarding, providing a responsive experience for users.

The below diagram explains how Sonarqube works with the above key components - SonarQube Server, Code Repository, SonarQube Scanner, Database, and Rules.

![image](https://github.com/user-attachments/assets/41707f6b-9545-418d-8d0e-6d6816b47bea)

1. Code Analysis:

The Code Repository and SonarQube Scanner are grouped under Code_Analysis because they are involved in the code analysis process. The repository holds the source code, and the scanner analyzes it.

2. SonarQube Server:

The SonarQube Server, Rules, Database, and ElasticSearch are grouped under SonarQube_Server because they collectively handle the processing, storage, and presentation of the analysis results.
Rules are defined within the server and used by the scanner during the analysis.
The Database stores the results, and ElasticSearch indexes the data for quick retrieval.

Interactions:

The scanner retrieves code from the repository and performs the analysis, then prepare the metrics and send the metrics to Sonarqube server. The analysis is based on the rules configured.
The server processes the metrics and stores the results in the database.
The processed data is indexed by ElasticSearch to make it searchable.
Users can then access the results through the SonarQube Server's web interface.

![image](https://github.com/user-attachments/assets/254bfd56-9ae0-495f-bb15-84a2549354b7)

Reference
https://www.youtube.com/watch?v=r2UVTDpIUj8
https://www.youtube.com/watch?v=6vdRvz_LnbQ
https://www.youtube.com/watch?v=Daq2BiacwjY&list=PLubBlcpiVEqw6AhvTGPjbOiQU-WPHom4_

STEP 1: 
https://hub.docker.com/_/sonarqube
docker pull sonarqube

![image](https://github.com/user-attachments/assets/83de49ac-9be6-4819-91de-cd74fbe0845c)

STEP 2:
https://hub.docker.com/r/jenkins/jenkins
docker pull jenkins/jenkins

![image](https://github.com/user-attachments/assets/27e76460-36a8-415b-a7f3-f0ac8775c8c4)

STEP 3:
Access Sonarqube
Default username/password will be admin/admin

![image](https://github.com/user-attachments/assets/0320cd10-3e00-430a-ade2-301260d12ae2)

forcefully changes the password [admin/admin1]

![image](https://github.com/user-attachments/assets/7b2f1ca1-1a58-4391-acc6-d179e1fe57fe)

Sonarqube UI looks like below

![image](https://github.com/user-attachments/assets/4e49894e-cbdb-4015-ba41-1861d19713a6)

Quality Profiles contains rules for each language which will be used for evaluating the code. You can customise the rules by copy and creating a new custom profile rule.

![image](https://github.com/user-attachments/assets/7f4fb657-bbe9-4778-91ce-456dc910f220)

If you want to add new languages, Administration -> Marketplace




**Running the Jenkins docker image**

PS E:\> `docker run -it -p 8080:8080 -p 50000:50000 jenkins/jenkins`               

![image](https://github.com/user-attachments/assets/d221ad19-0fdd-445c-8b34-3a8801d85d1a)

use the password here

![image](https://github.com/user-attachments/assets/8c4a5b6a-2efc-46e7-989c-133b2477b1d5)

access the jenkins using `http://localhost:8080`

![image](https://github.com/user-attachments/assets/daee88ee-2b47-403f-a252-08b9462e3393)

Click Next and Install the 'Suggested Plugins'

Jenkins is ready to use [user_one / user_one]

![image](https://github.com/user-attachments/assets/32e6d4d4-9261-44b5-b3fd-3318881d4d96)

To install Plugin for Sonarqube scanner, go to 

![image](https://github.com/user-attachments/assets/ee819595-23da-4413-a8aa-73161fcc7615)
![image](https://github.com/user-attachments/assets/f92a1ac9-d35c-40fe-8167-be428b9b9a17)

docker container ls -a

docker container start <container-id>

Configure the tools in Jenkins

![image](https://github.com/user-attachments/assets/a1d42cf8-dd58-4d5a-8116-896291e65f11)

![image](https://github.com/user-attachments/assets/95a2e4b6-e564-47f9-888c-9b6739617084)

before configuring maven, check the maven -version installed in your machine
![image](https://github.com/user-attachments/assets/e3c9872a-8985-412c-af76-95a5765dce8d)
