## 🧲 Integration of Sonarqube in Jenkins server with CodeQuality Review

In this small project, we will see how to Integrate the Tool **SonarQube Quality Code Analyser** to a Jenkins server and run a sample pipeline from Jenkins which will provide our GitHub code to SonarQube for Analysis. 

#### Pre-requisites:
- A sonarQube server running (``Mine is running in a local virtual machine at `` *192.168.38.152:9000*  )
- A Jenkins server running at (*192.168.38.90:8082* )
- A GitHub repo Code for Analysis.


### Step 1: Configuration on SonarQube server:

1. We need a user token of the Sonarqube server , and for this case we use the admin of our server.
2. So on the SonarQube server, by going to ``Administration > Security > Users `` and select the admin user for token generation .

![getTopken](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s2-sonar-token.png)
    
    `` Copy the token because we will provide this to Jenkins for integration. ``


### Step 2: Configuration on the Jenkins server:

Now let's see some configuration to be done on the Jenkins Server..

#### 1. Install the SonarQube plugin
    
- By going to ``Dashborad > Manage Jenkins > Plugin Manager > Available plugins `` and  search for *Sonarqube Scanner* :

![s1-plugin](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s1-plugin.png)

#### 2. Configuring the SonarQube credentials for the installed plugin 

- 2.1 *Configure the sonarQube server and token for Jenkins*
    - We have to provide the location of sonarqube server for jenkins.So we have to go to `` Dahsboard > Manage Jenkins > Configure System ``: 
        * Now provide a *Name* for sonarqube installation
        * The server URL
        * And server authentication Token (`The Token configured for the user of sonarqube server`)

        ![addingToken](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s4-adding-sonar-token.png)
        
        * And save.

    - Review final : 

        ![servver auth](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/sonar-serverNew.png)

        :exclamation: :exclamation: Depending on the Version of SonarQube , sometimes we got **`` API error ``** the server URL on the above configuration should be ``http://ip:9000 ``  , without the slash **( / )** at the end. So in case you get error while runnning the pipeline, review this part.

- 2.2  *Adding the sonarQube Scanner for Jenkins*  by going to `` Dahsboard > Manage Jenkins > Global Tool Configuration ``, and search for Section `` SonarQube Scanner ``.

![](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/s3-config-sonar.png)


### Step 3: Run a Job for SonarQube Analysis :

- 3.1 Create a new  pipeline Job  named `sonarqubePipeline`

- 3.2 After nothing to do , jump to pipeline section where we have to define our pipeline execution. We followed this pipeline script at: https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/jenkinsfile  
- 3.3 Apply and Save.

![newPipeline](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/pipe-build.png)

- 3.5 After pipeline execution, we can see on the console output the SonarQube URL is given to access the Analysis report.

![out1](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/consol-out1.png)

![out2](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/consol-out2.png)

- Final Pipeline view after execution:

![dashboard](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/dashb-pipelin.png)

### Viewing the result on SonarQube Dashboard server 
With the url given on the console output , we see:
![reviewcode](https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkins_pipeline_sonarQube/Screenshots/sonarQ-reviewcode.png)


In the next project we will see how to setup a Quality Gate in SonarQube and integrate it with Jenkins.

##### Our Integration is done 🤸 ... Time to take a Coffee 💥💥 !!! HAPPY LEARNING !!!! 