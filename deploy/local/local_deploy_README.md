# Local environment deploy on Tomcat and H2 database README

Make sure the initial steps for local code setup is followed.

#Prerequisites

##Environment Requirements:  
  Apache Tomcat 7.0.65

##1. Configure Tomcat

   1. Install Tomcat.  
   
   2. Open the **tomcat-users.xml** file under ..\apache-tomcat-7.0.65\conf and add below  role and users

        <role rolename="manager-gui"/>
        <role rolename="manager-script"/> 
        <user username="tomcat" password="tomcat" roles="manager-gui,manager-script"/>

   2. Add the following entry within servers definition in settings.xml of maven (.m2 folder) :
        
        <server>
         <id>TomcatServer</id>
	       <username> tomcat </username>
	       <password> tomcat </password>			
        </server>
        
   3. Add the following entry in settings.xml of Maven(.m2 folder)
   
        <pluginGroups>
	           <pluginGroup>org.apache.tomcat.maven</pluginGroup>
        </pluginGroups>     
        
   4. Goto bin folder in tomcat and run **start.bat** to start the tomcat server.        


##2. Deploy Project on Tomcat server
    
1. Open the pom.xml in webapps folder (/ArchetypeProject/webapps).
2. Add the tomcat-maven-plugin entry under build tag     
     
    ```xml
        <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <!-- or if you want to use tomcat 6.x
                <artifactId>tomcat6-maven-plugin</artifactId>
                -->
                <version>2.0</version>
                <configuration>
                    <!-- http port -->
                    <port>8080</port>
                    <!--<tomcat-url>http://localhost:8080/manager/html</tomcat-url>-->
                    <!-- application path always starts with /-->
                    <!--<path>/${project.build.finalName}</path>-->
                    <path>/${project.artifactId}</path>
                    <server>TomcatServer</server>
                    <url>http://localhost:8080/manager/text</url>
                    <!-- optional, needed only if you want to use a preconfigured server.xml file -->
                    <!-- <serverXml>${project.build.testOutputDirectory}/configuration/tomcat/server.xml</serverXml>-->
                    <!-- optional path to a context file -->
                    <contextFile>${configuration.dir}/target/configuration/local/tomcat/${project.artifactId}/context.xml</contextFile>
                    <!-- optional system propoerties you want to add -->
                    <systemProperties>
                        <!--<PORTALSERVER_PROXY_URL>http://localhost:8082/mashupservices-webapp/prox</PORTALSERVER_PROXY_URL>-->
                        <portalserver.language>en</portalserver.language>
                    </systemProperties>
                    <!-- if you want to use test dependencies rather than only runtime -->
                    <useTestClasspath>false</useTestClasspath>
                    <!-- optional if you want to add some extra directories into the classloader -->
                    <additionalClasspathDirs>
                        <additionalClasspathDir></additionalClasspathDir>
                    </additionalClasspathDirs>
                    <addContextWarDependencies>true</addContextWarDependencies>
                    <addWarDependenciesInClassloader>true</addWarDependenciesInClassloader>
                    <warSourceDirectory>${project.build.directory}/${project.build.finalName}/</warSourceDirectory>
                </configuration>
                <!-- For any extra dependencies needed when running embedded Tomcat (not WAR dependencies) add them below -->
                <dependencies>
                    <dependency>
                        <groupId>javax.mail</groupId>
                        <artifactId>mail</artifactId>
                        <version>1.4.3</version>
                    </dependency>
                    <dependency>
                        <groupId>com.h2database</groupId>
                        <artifactId>h2</artifactId>
                        <version>1.3.154</version>
                    </dependency>
                </dependencies>
            </plugin>
    ```
                                                                                      
    Refer to [pom.xml](https://github.com/github-sumit/fin_banking_bb/blob/master/deploy/local/pom.xml) in the files folder. 
            
3. Goto the base project.   
    **e.g.:** /Archetype

5. Deploy the project to tomcat using the following maven command.
        
        mvn clean package tomcat7:deploy
        
    Make sure the tomcat server is started before executing this command.
        
6. Stop the tomcat server.
>   **Note:** This step is required because tomcat will throws error while deployment of required modules as the context files are missing.Ignore the errors
              and continue with the further steps.
  
7. Copy the context file from the project configuration path to apache-tomcat-7.0.65\conf\Catalina\localhost in tomcat and rename to the following names:
  
  | Project  	| Path in configuration folder  	| Context file name in tomcat   |  
  |:--------:|:----------------------------:|:---------------------------:|  
  | portalserver	   | fip_banking_bb-master\configuration\ target\ configuration\local\tomcat\portal 	| portalserver.xml  |  
  | contentservices 	  | fip_banking_bb-master\configuration\ target\configuration\local\tomcat\contentservices  | contentservices.xml  |   
  | orchestrator    | fip_banking_bb-master\configuration\target\ configuration\local\tomcat\orchestrator  |  orchestrator.xml  |  
        
8. Start the tomcat server.
  
9.	The application should be deployed.

10.	Open the following URL 
   
        http://localhost:8080/manager/html
        
11.	Enter the username/password in the pop-up, mentioned in **tomcat-users.xml**  file.
  
12.	Applications should be successfully deployed in tomcat manager with **Running** value as true .
  
13.	Once the server is successfully started, open the following  URL:
  
       http://localhost:8080/portalserver/
