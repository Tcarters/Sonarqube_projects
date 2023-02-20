## 🚀 Learning SonarQube Tool Integration with Jenkins CI/CD  

### What is SonarQube ?

- SonarQube is an Open Source web based tool to manage code Quality and Code Analysis. It is most widely used in continuous code inspection which performs reviews of code to detect bugs, code smells and vulnearibilty issue of programming languages such as PHP, C#, JavaScript, C/C++ and Java.
- It also tracks statistics and creates charts that enable developers to quickly identify problems in their code.
- SonarQube help catch tricky bugs to prevent undefined behavior from impacting end-users.
- It also fix vulnerabilities that compromise your application.
- SonaQube makes sure our codebase is clean and maintainable, to increase developer velocity...


### SonarQube Features
- It's Open Source.
- Code Coverage
- Enhance dev Workflow with Continuous Code Quality & Code Security.
- Compatible with various programming languages.
- Detects redundancy in code , unit test cases, integration test cases.
- Generate reports after testing.
- Easily integrates with build tools like : 
```diff 
- Ant, Maven, gradle , Eclipse, etc ...
```


### Why  PostgreSQL ?
- PostgreSQL is an Open Source and powerfull object-relational database.
- The relational database is the most common type of database in use.
- PostgreSQL has very good performance and very much reliable.
- PostgreSQL also works well with Business Intelligence Applications but is better suited for Data Warehousing and Data Analysis Applications that require fast read/write speeds.
- PostgreSQL is complet ACID (Atomicity - Consistency - Isolation and Durability ) , compliant .


### What is Sonar Scanner ?

> With SonarQube  Scanner or sonar-scanner, we can : 
>    -  performs the code Analysis, 
>    -  generate the results and sends the results to SonarQube.

>  SonarQube is a genric CLI scanner, and we must provide configurations that list the locations of the source files, test files, SonarQube  host and URL with a domain name.   


### How can we Integrate sonarQube with Jenkins using Maven Job ?

- Installation & Configuration of PostgreSQL
- Installation & Configuration of SonarQube
- Installation & Configuration of  Sonar Scanners.
- Installation & Configuration of SonarQube SCANNER Plugin to integrate Jekins with SonarQube
- Adding SonarQube tool
- Generating token for Jenkins.
- Configuring SonarQube Server
- Creating new Maven Job
- Adding analysis properties and goal.


## SonarQube Quality Gates Feature 
- Quality Gates in SonarQube is a set of conditions that must be met in order for a project to be marked  as **passed**. 
-  Basically Quality Gates enforces the **Quality Policy** for an application through some conditions defined in the policy and then this will prevent us to deploy an application containing some bugs or vulnerabilities ...   
### SonarQube Quality Gates working
<!-- /* ![](pic to be added ) */ -->
 
### Minor Projects with SonarQube