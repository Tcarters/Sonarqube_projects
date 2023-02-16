## Integration of Sonarqube in Jenkins server with CodeQuality Review


#### Pre-requisites:
- A sonarQube server running (``Mine is running in a local virtual machine at *192.168.38.152:9000*`` )
- A Jenkins server running at (**192.168.38.90:8082** )


### Step 1: Configuration on SonarQube server:

1. We need a user token of the Sonarqube server , and for this case we use the admin of our server.
2. So on the SonarQube server, by going to ``Administration > Security > Users `` and select the admin user for token generation .

![](sonar-token)
    
    `` Copy the token because we will provide this to Jenkins for integration. ``


### Step 2: Configuration on the Jenkins server:

#### 1. Install the SonarQube plugin
    
- By going to ``Dashborad > Manage Jenkins > Plugin Manager > Available plugins `` and  search for *Sonarqube Scanner* 
![s1-plugin]()

#### 2. Configuring the SonarQube credentials for the installed plugin 

- 2.1 *Configure the sonarQube server and token for Jenkins*
    - We have to provide the location of sonarqube server for jenkins.So we have to go to `` Dahsboard > Manage Jenkins > Configure System ``: 
        * Now provide a *Name* for sonarqube installation
        * The server URl
        * And server authentication Token (`the Token configured for the user of sonarqube server`)
            ![servver auth]() 
        * And save.

- 2.2  *Install the sonarQube Scanner for Jenkins*  by going to `` Dahsboard > Manage Jenkins > Global Tool Configuration ``, and search for Section `` SonarQube Scanner ``.
![](sonar-scanner)


### Step 3: Run a Job for SonarQube Analysis :

- 3.1 Create a new  pipeline Job  named `sonarqubePipeline`

- 3.2 After nothing to do , jump to pipeline section where we have to define our pipeline execution. We followed this pipeline script at :https://github.com/Tcarters/Sonarqube_projects/blob/master/jenkinsfile  

![]()
- 3.3 Apply and Save .

- 3.5 After pipeline execution, we can see on the console output the SonarQube URL is given to access the Analysis report .
![out1]()
![out2]()

### Viewing the result on SonarQube Dashboard server 
With the url given on the console output , we see :
![reviewcode]()



##### Our Integration is done 🤸 ... Time to take a Coffee 💥💥 !!! HAPPY LEARNING !!!! 