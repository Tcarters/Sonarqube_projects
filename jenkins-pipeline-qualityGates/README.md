# Using SonarQube Quality gates to our Jenkins build pipeline

Let's see in this mini project how to implement sonarQube Quality Gates conditions in a Jenkins pipeline Job.

<img src="https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/Quality-Gates_Uses-cases.jpg" width ="45%" height="50%"/>

### Scenarios:
- Create a custom Quality Gate condition
- Configure a webHook in the SonarQube server
- Integrate with Jenkins with SonarQube Server
- Create a Job and implement 2 cases test (*Passed & Failed*) for the Defined Quality Gate Condition.

### Pre-requisites:
- A Jenkins Server running in a Single Machine
- Another machine running SonarQube Server 
- A GitHub repository 

- - -
## Step 1:  Configuration of SonarQube Server:

- 1. To allow Jenkins to connect to the SonarQube Server, we have to provide the SonarQube Server token to Jenkins. And in our case we have a default user **admin**, so let's gave his token to Jenkins. `` In real case production for security purpose, provide the token of a standard user``. 
- 2. So to do it, on the SonarQube server, let's go to ``Administration > Security > Users `` and select the admin user for token generation .

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s2-sonar-token.png)
    
    `` Copy the token because we will provide this to Jenkins for integration. ``

## Step 2:  Create a custom Quality Gate Condition in the SonarQube Server configuration :

- To create a quality gate condition, we go to: ``SonarQube Dashboard > Quality Gates > `` 

![defaultqg](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/defaultqg.png)

- Click on the button, `` Create`` to create a new:

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/nameQG.png)

- After give a name for the new quality Gate, save it.

- Now we have to add a Condition :
    - Select  ``On New Code`` for new project that will be integrated to sonarqube platform
    - After we choose as metrics `` Bugs`` and ``Vulnerabilities, Major Issues , Critical Issues `` ,etc... :firey **Add more as you want**
    - And after click on button: ``Add Condition ``

    ![cond](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/qG-metric1.png)

- Finally, set the new Quality Gate as: `` Default ``

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/customQG.png)

## Step 3:  Configure a webhook in sonarQube server :

- With a WebHook configuration, we allow Sonarqube to notify us about every project analysis completion ...

- To configure it we will go on SonarQube Dashboard : ``Administration > Configuration > Webhooks ``
    * Now give a **Name** of the new webhook
    * Put the URL of the ``Jenkins server `` + the important part *sonarqube-webhook/* 

![s6-webhook](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/s6-webhookjenk.png)

![s6-webhook2](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/s6-webhook2.png)


## Step 4: Configurations on Jenkins Server:

- 4.1 Firstly, Install the SonarQube Scanner plugin 
    - By going to ``Dashborad > Manage Jenkins > Plugin Manager > Available plugins `` and  search for **Sonarqube Scanner for Jenkins**. 
    - After select, install without restart.

![s1-plugin](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s1-plugin.png)

- 4.2 Configure the sonar server properties 

    - We go to `` Dahsboard > Manage Jenkins > Configure System `` , and look for the Section `` SonarQube servers``: 
        * Now provide a **Name** for sonarqube installation **(It can be anything , here I use the name of SonarQube server version )**
        * The server URL `` http://ip:9000``
        * And Server Authentication Token (`The Token configured earlier for the user of sonarqube server`)

        ![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s4-adding-sonar-token.png)
     
        * And save.
    
    - Review final : 
        ![server auth]( https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/sonar-serverNew.png )
    
    :exclamation: :exclamation: A mistake in the above pic , URL should be: **http://ip-server:port-no** without the end **slash /**
    
- 4.3 Add the SonarQube Scanner plugin     
    - Following the path `` Dahsboard > Manage Jenkins > Global Tool Configuration ``, and search for Section `` SonarQube Scanner ``.

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s3-config-sonar.png)


- 4.4 Now, add the sonarQube properties
    - We need **sonar-project.properties** to configure Sonar with a specific application. This part is required when we have a big projects with lots of dependencies and we want some specific check for our application.

    - The configuration for the `` Required metadata`` will be:

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
    - In this project, we didn't implement this cause we have already configured manually sonarqube scanner properties in Jenkins system parameters.

## Step 5: Create a new Pipeline job on Jenkins server with our custom Quality Gate defined:

- Create a new sample pipeline job:

    ![img-job](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/qg-job1.png)

- After, jump directly to the section ``Pipeline`` and define a pipeline script like the on used :

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
                git 'https://github.com/Tcarters/HelloSpringJenkins'
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
                timeout (time: 5) { // Just in case something goes wrong, pipeline will be killed after a timeout
                     // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    def myqualitygate = waitForQualityGate abortPipeline: true 
                    // Reuse taskId previously collected by withSonarQubeEnv
                    if ( myqualitygate.status != 'OK' ) {
                        echo "Pipeline aborted due to Quality Gate failure 🎃: ${myqualitygate.status}"
                        }
                    else { 
                        echo "Pipeline  succeeded with Quality Gate 🤗 : ${myqualitygate.status}"
                    }
                }
            }
        }
    }
}

```
- Now click ``Build Now`` to start building the pipeline job. 

### Result 
- we can see that our pipeline executing aborted due to our defined timeout reached , and we have to mentionned that Quality Gate work like a condition meaning that after scanning the code received from Jenkins if the result is OK then the pipeline proceeed in next steps but if the scanning report an error like bugs, vulnerabilities then the pipeline will be failed with a retry in the timeout defined.
![res1](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/res1-qg.png)

- Now on the Dashboard of jenkins server, after the pipeline completed, we got all test passed.

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/res2-qg.png)

- :exclamation: :exclamation: Feel free to change the timeout and github repo code in Jenkins script pipeline to deal better with SonarQube scanner.

### Step 6:  Check for a failed condition from sonarqube server 
- For that, create another pipeline `projectQG2` with a different git repo, ( we used: https://github.com/shazforiot/GOL.git ).

- And change our custom Quality Gate Condition like below:

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/res3-qg-condt.png)

- Use the jenkins script defined in the file `` jenkinsfile-for-failed-check `` available at: https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/jenkinsfile-for-failed-check

- On the SonarQube server, we see the Analysis failed because the submitted code doesn't respect our defined **Quality Gate Condition**.

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/res-QGfailedpip.png)

- Returning on the Jenkins Pipeline view Dashboard:

    - On the Console Output of the build pipelne, we got:

    ![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/failureproject2-sonar.png)

![resQG-failed](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins-pipeline-qualityGates/Screenshots/res-finalQG2-failed.png)


### Tadaaa, we're Done with this Integration ...