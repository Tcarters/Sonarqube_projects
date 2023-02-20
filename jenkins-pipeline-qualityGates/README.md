# Using SonarQube Quality gates to our Jenkins build pipeline

### Scenarios:
- Create a custom Quality Gates
- Integrate with Jenkins 
- Test cases with policy defined in our Quality Gates feature

### Pre-requisites:
- A Jenkins Server running
- SonarQube Server running

## Configuration of SonarQube Server:

- 1. To allow Jenkins to connect to the SonarQube Server, we have to provide the SonarQube Server token to Jenkins. And in our case we have a default user **admin**, so let's grave his token. `` In real case production for security purpose, provide the token of a standard user``. 
- 2. So on the SonarQube server, by going to ``Administration > Security > Users `` and select the admin user for token generation .

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s2-sonar-token.png)
    
    `` Copy the token because we will provide this to Jenkins for integration. ``

## Create a custom Quality gate in SonarQube Server :

- To create a quality gate condition, we go to: ``SonarQube Dashboard > Quality Gates > `` 
![defaultqg](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/defaultqg.png)

- Click on the button, `` Create`` to create a new 
- After give a name for the new quality Gate
- Now add a Condition :
    - Select  ``On New Code`` for new project that will be integrated to sonar
    - After we choose as metrics `` Bugs`` and ``Vulnerabilities, Major Issues , Critical Issues `` 
    - And after click ``Add Condition ``
    - ![cond-QG]
- Finally , set the new Quality gate as `` Default ``

## Configure a webhook in sonarQube server :

- With this configuration , we allow Sonarqube to notify us about every project analysis completion ...
- To configure it we will go on SonarQube Dashboard : ``Administration > Configuration > Webhooks ``
    * Now give a `` Name `` of the new webhook
    * Put the URL of the ``Jenkins server `` + the important part ``sonarqube-webhook/ `` 
![s6-webhook]()
![s6-webhook2]()


## Configuration of Jenkins Server:

- 2.1 Install the SonarQube Scanner plugin 
    - By going to ``Dashborad > Manage Jenkins > Plugin Manager > Available plugins `` and  search for *Sonarqube Scanner for Jenkins*. After select , install without restart.

![s1-plugin](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_sonarQube_CodeQuality/Screenshots/s1-plugin.png)

- 2.2 Configure the sonar server properties 
    - We go to `` Dahsboard > Manage Jenkins > Configure System `` , and look for the Section `` SonarQube servers``: 
        * Now provide a *Name* for sonarqube installation **(It can be anything , here I use the name of SonarQube server version )**
        * The server URL `` http://ip:9000``
        * And Server Authentication Token (`The Token configured earlier for the user of sonarqube server`)

        ![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_sonarQube_CodeQuality/Screenshots/s4-adding-sonar-token.png)
     
        * And save.
    
    - Review final : 
        ![servver auth]( https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_sonarQube_CodeQuality/Screenshots/sonar-serverNew.png )
    
- 2.3 Add the SonarQube Scanner plugin     
    - Following the path `` Dahsboard > Manage Jenkins > Global Tool Configuration ``, and search for Section `` SonarQube Scanner ``.

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_sonarQube_CodeQuality/Screenshots/s3-config-sonar.png)


- 2.4 Now, add the sonarQube properties
    - We need **sonar-project.properties** to configure Sonar with a specific application. This part is required when we have a big projects with lots of dependencies and we want some specific check for our application.
    - The configuration for the `` Required metadata`` will be :

        ```
        sonar.projectKey=repo-name  # It can be anything or your project name..
        sonar.projectName="A sample Project for testing Quality Gate from jenkins" ## Can     ##be anything
        sonar.projectVersion=1.0

        #Comma-separated path to directories with sources(required)
        sonar.sources=src  # path to the main code
        #Defining language
        sonar.language=java

        #Encoding of the source files
        sonar.sourceEncoding=UTF-8

        ```

## Create a new Pipeline job on Jenkins server with Our custom Qualty Gate defined:

- Create a new sample pipeline job

    ![img-job]()

- Jump directly to the section ``Pipeline`` and define a pipeline script like the on used :

```
pipeline {
    agent any 
    environment {
        PATH = "$PATH:/usr/share/maven/bin" //On my local Jenkins maven is installed there.
    }
    stages {
        stage (' Clone Code from GitHub') {
            steps {
                echo '<--------- Starting pulling the code -------------->'
                git 'https://github.com/SonarSource/sonar-scanning-examples'
                //'https://github.com/Tcarters/HelloSpringJenkins.git'
                echo '<------------Code pulling ended---------------------->'
            }
        }
        stage ('Sample SonarQube Analysis') {
            // def scannerHome = tool 'SonnarScanner 4.8' ; No need because we setup an automatically install of this tool
            steps {
                withSonarQubeEnv('sonarqubeServer9.1') {
                    sh 'mvn sonar:sonar'
                    // or can use sh ' mvn clean package sonar:sonar '
                }
            }
        }
        stage ('Quality Gates Analysis') {
            steps {
                echo '<---------------Quality Gates Analysis Starting-------------->'
                timeout (time: 2) { // Just in case something goes wrong, pipeline will be killed after a timeout
                     // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    def myqualitygate = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                    if ( myqualitygate.status != 'OK' ) {
                        error "Pipeline aborted due to Quality Gate failure 🎃: ${myqualitygate.status}"
                        }
                    else { 
                        error "Pipeline  succeeded with Quality Gate 🤗 : ${myqualitygate.status}"
                    }
                }
            }
        }
    }
}

```
- Now click ``Build Now`` to start our pipeline 

### Result 
- we can see that our pipeline executing aborted due to our defined timeout reached , and we have to mentionned that Quality Gate work like a condition meaning that after scanning the code received from Jenkins if the result is OK then the pipeline proceeed in next steps but if the scanning report an error like bugs, vulnerabilities then the pipeline will be failed with a retry in the timeout defined.
![res1]()
- But from our jenkins server all test passed 
- So feel free to change the timeout and github repo code in Jenkins script pipeline to deal better with SonarQube scanner

### Check for a failed condition from sonarqube server 
- For that, create another pipeline `projectQG2` with a different git repo, we see here also our Qualitty chek pass:

- And change our custom Quality gate condition 
- Use the jenkins script defined in the file `` jenkinsfile-for-failed-check ``
![resQG-failed]()