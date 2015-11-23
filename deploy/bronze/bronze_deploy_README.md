# Local environment deploy on Tomcat and MySQL database README

Make sure the initial steps for local code setup is followed.

##1. Configure Tomcat

   1. Open the **tomcat-users.xml** file under ..\apache-tomcat-7.0.65\conf and add below  role and users

        <role rolename="manager-gui"/>
        <role rolename="manager-script"/> 
        <user username="tomcat" password="tomcat" roles="manager-gui,manager-script"/>

   2. Add the following entry within servers definition in settings.xml of maven :
        
        <server>
         <id>TomcatServer</id>
	       <username> tomcat </username>
	       <password> tomcat </password>			
        </server>
        
   3. Goto bin folder in tomcat and run **start.bat** to start the tomcat server.    
   
##2. Add Tomcat server to required modules
    
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
                </dependencies>
            </plugin>
    ```
                                                                                      
    Refer to [pom.xml](https://github.com/github-sumit/fin_banking_bb/blob/master/deploy/local/pom.xml) in the files folder.        

##3. Setup MySQL database 

   1. Install MySQL workbench 6.5  
    
   2. Goto  ..\MySQL\MySQL Server 5.6  and edit **my.ini** file.  
      (In my case, the file is named **my-default.ini**)  
         
   3. Add the following to the **[mysqld]** section of the MySQL configuration file:  
        
        default-storage-engine = innodb  
        transaction-isolation = READ-COMMITTED  
        init_connect='SET collation_connection = utf8_general_ci'  
        init_connect='SET NAMES utf8'  
        character-set-server=utf8  
        collation-server=utf8_general_ci  
        skip-character-set-client-handshake  
        
   4. Add the following to the **[mysql]** section of the MySQL configuration file, or replace the property value if it is already defined:
   
        [mysql]
        default-character-set=utf8
        
   5. Save the file.
   
   6. If the file gives  error while saving,then open the file as admin using below steps in Windows environment:  
   
      •	Open the command prompt as administrator.  
        (cmd.exe -> right click -> Run as Administrator)  

      •	On the command prompt, go to ..\MySQL\MySQL Server 5.6 folder and execute the following command:   

             notepad my-default.ini   
             

      This will open the my.ini/my-default.ini file for editing in notepad.  

      •	Add the entries mentioned in point iii and iv and save the file.  
      
   7. Restart MySQL sever   
      •	In Windows environment,   
        Goto Start  -> Right Click on Computer -> click on Manager.

      •	Click on Services and Applications in Computer Management window.

      • Click on Services -> Right Click on MySQL and restart the server.   
      
   8. Log on to MySQL Workbench with user having admin rights.
   
   9. Create 5 schemas for Backbase.  
   
    | **Schema**  	| **Project**  	|     
    |:--------:|:------------------:|    
    | portalfoundation	   | portalserver 	|     
    | contentservices 	  | contentservices  |    
    | dbresources    | contentservices  |    
    | configuration    | contentservices  |    
    | orchestrator    | orchestrator  |      
   
   10. Double-click on each of the schema name to connect and run the **.ddl** and **.sql** scripts to populate the database respectively.  
   
   11. The scripts are located at the following path:  
      
   | **Project**  	| **Path**  	|   
   |:--------:|:------------------:|  
   | portalserver	   | \configuration\src\main\resources\db-scripts\portalserver\scripts\mysql 	|   
   | contentservices  | \configuration\src\main\resources\db-scripts\contentservices\mysql  |   
   | orchestrator    | \configuration\src\main\resources\db-scripts\orchestrator\scripts\mysql  |  
      
>    **Note:** For contentservices schema,run the .ddl and .sql file at the mentioned location for each of the schemas.

##4. Add MySQL dependency in the required modules.

   1.	Open the **pom.xml** file in the \webapps folder and add the following dependency to the **tomcat7-maven-plugin** definition:
   
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.9</version>
        </dependency>
        
      Remove the H2 db dependency,if exists.  
      
   2. Remove the whole sql-maven-plugin plugin definition from the pom.xml of the below modules  
    
      •	Portalserver  
      •	Contentservices  
      •	Orchestrator    
      
   3. Create **local** properties file for MySQL at \configuration\src\main\filters location.
      Refer to [local.properties](https://github.com/github-sumit/fin_banking_bb/blob/master/deploy/bronze/local-mysql.properties) file.  
      
>   **Note:** Update the database username and password as per the settings. 

   4. Open the pom.xml file in configuration project and replace the entry for **filter** under **maven-resources-plugin**  definition for id **local** with the properties file for MySQL.   
   
      **Eg:** <filter>${basedir}/src/main/filters/local-mysql.properties</filter>  
   
      ```xml   
        <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <executions>
                    <execution>
                        <id>local</id>
                        <phase>process-resources</phase>
                        <goals>
                            <goal>resources</goal>
                        </goals>
                        <configuration>
                            <encoding>UTF-8</encoding>
                            <outputDirectory>${work.dir}/local</outputDirectory>
                            <filters>
                               <!--  <filter>${basedir}/src/main/filters/local.properties</filter> -->
                                <filter>${basedir}/src/main/filters/local-mysql.properties</filter>
                            </filters>
                        </configuration>
                    </execution>
      ```  
      
   5.	Copy [mysql-connector](https://github.com/github-sumit/fin_banking_bb/blob/master/deploy/bronze) jar to the lib folder of tomcat.   
      (This step is required so tomcat server can connect to MySQL database)    
      
      
##5. Deploy required modules to tomcat  
            
   1. Goto base project and deploy the project to tomcat using the following maven command.
        
          mvn clean package tomcat7:deploy
        
      Make sure the tomcat server is started before executing this command.
        
   2. Stop the tomcat server.
>   **Note:** This step is required because tomcat will throws error while deployment of required modules as the context files are missing.Ignore the errors
              and continue with the further steps.
  
   3. Copy the context file from the project configuration path to apache-tomcat-7.0.65\conf\Catalina\localhost in tomcat and rename to the following names:
  
    | **Project**  	| **Path in configuration folder**  	| **Context file name in tomcat**   |  
    |:--------:|:----------------------------:|:---------------------------:|  
    | portalserver	   | fip_banking_bb-master\configuration\ target\ configuration\local\tomcat\portal 	| portalserver.xml  |  
    | contentservices 	  | fip_banking_bb-master\configuration\ target\configuration\local\tomcat\contentservices  | contentservices.xml  |   
    | orchestrator    | fip_banking_bb-master\configuration\target\ configuration\local\tomcat\orchestrator  |  orchestrator.xml  |  
        
   4. Start the tomcat server.
  
   5.	The application should be deployed.
  
   6.	Open the following URL 
   
        http://localhost:8080/manager/html
        
   7.	Enter the username/password in the pop-up, mentioned in **tomcat-users.xml**  file.
  
   8.	Applications should be successfully deployed in tomcat manager with **Running** value as true .
  
   9.	Once the server is successfully started, open the following  URL:
  
          http://localhost:8080/portalserver/
