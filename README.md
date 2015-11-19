# fin_banking_bb

Digital Portal for Banking.
The project can demonstrate the capability of deploying portal into Virtual environment.
Backbase allows to develop widgets based portal.

# Deployment Architecture



#Prerequisites

##Environment Requirements:

1. install Virtual Box
2. install Vagrant
3. IntelliJ (https://confluence.jetbrains.com/display/IDEADEV/EAP) or Eclipse
4. Install Java 1.7
5. Maven 3.2.5 or higher
6. Git (https://nodejs.org/en/)
7. Nodejs and npm (https://nodejs.org/en/)
8. Bower
7. Apache Tomcat 7.0.65


###Environment variables
1. JAVA_HOME = jdk_home_directory
    On Windows environment,
      Right click on My Computer -> Properties -> Advance system settings -> Environmental Variables. 
      **JAVA_HOME** = C:\Program Files\Java\jdk1.7.0_80

2. Set **M2_HOME** and **MAVEN_OPTS** in environment variables 
    M2_HOME = maven_home_directory
    Eg:-
      M2_HOME = C:\Users\DasID\..\apache-maven-3.2.5

    MAVEN_OPTS = -Xmx512m -XX:MaxPermSize=256m -XX:+UseConcMarkSweepGC
    
3. Set following in **PATH** variable in environment variables
    jdk_bin_directory = %JAVA_HOME%\bin  
    maven_bin_directory = %M2_HOME%\bin  = 
    npm = C:\Users\[user-name]\AppData\Roaming\npm (npm folder created at this location by default when nodejs is installed)
    git_bin_directory = C:\Program Files (x86)\Git\bin
    git_command_directory = C:\Program Files (x86)\Git\cmd
    
    Eg:-
        PATH = %JAVA_HOME%\bin;%M2_HOME%\bin;C:\Users\A613165\AppData\Roaming\npm;C:\Program Files (x86)\Git\bin;C:\Program Files (x86)\Git\cmd; 
    
    ###NOTE: npm path and git directory path is set while installation. Add the path if not present in PATH variable.         

5. Confirm environment variable settings. On Windows , open command prompt and execute below commands

      javac –version
      mvn –version
      npm version
      git  --version

6. Install Bower.
   On Windows, Open  command prompt and execute the command
   
         npm install -g bower


#Development Environment Setup

1. Create an **archetype** project using below command

      mvn archetype:generate -DarchetypeArtifactId=launchpad-archetype-CXP5.6 -DarchetypeGroupId=com.backbase.launchpad -DarchetypeVersion=1.0.2


2. Maven is going to prompt for some details:
    •	A group id – a unique package identifier, e.g. com.backbase.training.
    •	An artifact id – a name for your project. This is also the name of the folder where your project will be stored. e.g. FirstBackbaseProject
    •	A version number and a package name – you can accept the defaults.
    •	A launchpad edition – a collection to install; use **universal**.
    •	A launchpad version – a launchpad version to install. This article has been tested with version **0.13.1**.


3. Confirm the values entered by entering **y**, or type **n** to change them. After confirmation, a project structure will be created.


 The next steps in order to setup this project locally.
   
##1. Setup Maven Repository
    Open the settings.xml file under  maven local repository  folder (.m2) 
    ###NOTE: If settings.xml is not at the above location, then copy the file from files folder to .m2 folder.
    
   Add the backbase plugins and servers entries in settings.xml :
   <profiles>
    <profile>
      <id>backbase</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <!-- Main product repository -->
        <repository>
          <id>repo.backbase.com</id>
          <url>https://repo.backbase.com/repo/</url>
        </repository>
        <!-- Internal development repository is due to mobile services until it gets properly released -->
        <repository>
          <id>artifacts.backbase.com</id>
          <url>https://repo.backbase.com/repo/</url>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>repo.backbase.com</id>
          <url>https://repo.backbase.com/repo/</url>
        </pluginRepository>
      </pluginRepositories>
    </profile>
    <properties>
      <downloadSources>true</downloadSources>
      <downloadJavadocs>true</downloadJavadocs>
    </properties>
   </profiles>
   <!--and next to this tag the authorization config -->
   <servers>
    <!-- Main product repository -->
    <server>
      <id>repo.backbase.com</id>
      <!--Please change your_user_name and your_password below-->
      <username>atos_portal_p5</username>
      <password>atos_portal_1654</password>
    </server>
    <!-- Internal development repository is due to mobile services until it gets properly released -->
    <server>
      <id>artifacts.backbase.com</id>
      <!--Please change your_user_name and your_password below-->
      <username>atos_portal_p5</username>
      <password>atos_portal_1654</password>
    </server>
    <!-- This identity is due to bug with LP 0.10 that requires this -->
    <server>
      <id>Backbase Artifact Repository</id>
      <!--Please change your_user_name and your_password below-->
      <username>atos_portal_p5</username>
      <password>atos_portal_1654</password>
    </server>
   </servers>
   
##2. Perform build of all the modules 
    On Windows,go to the command prompt and run the maven command on the root project:

      mvn clean package -Pclean-database

    Make sure the build is successful.
 
##3. Prepare required modules for local development
    •	Orchestrator
    •	Contentservices
    •	Portalserver
    
    GoTo /webapps in base archetype project and run the default prepare script **a default prepare script ** for each module
    
##4. Run required modules 
    On Windows, goto base archetype project and run the default start script **run.bat**.
    
    Eg:
    C:\Workspace\Archetype> run.bat 
  

  The project will start on jetty server and H2 database.  
  Once the server is successfully started, open the following  URL:
        http://localhost:7777/portalserver/

##Module descriptions

Here is list if all Maven modules together with its description:

configuration contains all project configurations.
contentservices is customized *contentservices module able to run as standalone service.
dist produces distribution of all modules.
orchestrator is customized *orchestrator module able to run as standalone service.
portal is backbase portal module.
services is parent module for all Integration services.
statics is parent module for all web static assets packaged as Backbase bundles.    
cxp-exports is the cxp-manager launchpad.
      
 
  









      
      










            

  
