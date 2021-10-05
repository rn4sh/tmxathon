# Tmxathon

Criteria 1: Migration
=====

For this challenge, you’ll work with an existing Java EE application designed for a retail webshop. The current version of the webshop is a Java EE application built for Oracle Weblogic Application Server. As part of a modernization strategy you’ve decided to move this application to JBoss EAP, containerize it, and run it on a Kubernetes platform with OpenShift. The application is currently hsoted [here](https://github.com/RedHat-Middleware-Workshops/cloud-native-workshop-v2m1-labs.git) . Application in question in under the /monolith folder

To help do this, you can use the [Migration Toolkit for Applications](https://access.redhat.com/documentation/en-us/migration_toolkit_for_applications/5.2/html/introduction_to_the_migration_toolkit_for_applications/what-is-the-toolkit_getting-started-guide) IDE Plugin based on CodeReady Workspaces. 


Hints
=====
a. You can access your [CodeReady Workspaces instance]() and log in using the username and password you’ve been assigned (e.g. PLEASE ENTER YOUR USERID AT TOP OF PAGE/r3dh4t1!) </br>
b. Make sure to Git clone the complete [Repo](https://github.com/RedHat-Middleware-Workshops/cloud-native-workshop-v2m1-labs.git) </br>
c. The MTA tool is located under:
![image](https://user-images.githubusercontent.com/40291650/136104885-725d0eb6-61c6-41dc-96dd-844ac5725f0e.png)
d. The code for the application is located under /monolith - When configuring MTA, your iput will need to point this folder </br>
e. It's a Weblogic applcation that needs to migrated to Jboss EAP verion 7 </br>
f. After running the MTA report, your migration's issues will be shown as follows:
![image](https://user-images.githubusercontent.com/40291650/136105599-5ed121fa-5b29-41ba-b7b3-4566542a6da4.png)
g. Under the /migration folder in the current repo, you can find the migrated java classes needed (you can simply overwrite yours with those ones)</br>
h. The file weblogic-ejb-jar.xml is not longer needed and needs to be deleted </br>
k. The folder src/main/java/weblogic is not needed and needs to be deleted</br>
l. Remove the dependency org.jboss.spec.javax.rmi:jboss-rmi-api_1.0_spec from your application POM under monolith/pom.xml
m. When you finish fixing the migration issues - make sure you can build without any errors using: mvn -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith clean package</br>
n. Re-run and report - if no issues found, you're good to go

Criteria 2: Deploy your Monolith to OpenShift
=====
For this challenge, you need to deploy your migrated JBoss App to OpenShift

Hints
=====
a. Add an OpenShift Profile to your POM file under monolith/pom.xml - an example is provided this repo's /migration/openshift-profile</br>
b. Login to your OpenShift [Console](https://console.rh-us-east-1.openshift.com/) and create a new project</br>
c. Under Topology view, your ops team created a template that you can use, it's called Coolstore Monolith and containers your EAP runtime and DB - make sure you use your user ID</br>
d. You can deploy from your CodeReady Workspace instance - Make sure you're logged to OpenShift first, then switch to the project you just created using: oc project <your-project-name></br>
e. To see a nice topoly view, you can add labels to your deployments:
oc label dc/coolstore-postgresql app.openshift.io/runtime=postgresql --overwrite && \
oc label dc/coolstore app.openshift.io/runtime=jboss --overwrite && \
oc label dc/coolstore-postgresql app.kubernetes.io/part-of=coolstore --overwrite && \
oc label dc/coolstore app.kubernetes.io/part-of=coolstore --overwrite && \
oc annotate dc/coolstore app.openshift.io/connects-to=coolstore-postgresql --overwrite && \
oc annotate dc/coolstore app.openshift.io/vcs-uri=https://github.com/RedHat-Middleware-Workshops/cloud-native-workshop-v2m1-labs.git --overwrite && \
oc annotate dc/coolstore app.openshift.io/vcs-ref=ocp-4.7 --overwrite</br>
f. Deploy the application - build first, then deploy:</br>
  mvn clean package -Popenshift -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith</br>
  oc start-build coolstore --from-file $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith/deployments/ROOT.war --follow</br>

  
Criteria 3: Create a new Quarkus application
=====
For this challenge, you need to implement the Inventory project in Quarkus. Expand the /inventory folder in your application:</br>
![image](https://user-images.githubusercontent.com/40291650/136110070-4864704c-0187-449b-91f5-0a569432e95e.png)

You will find the implementation of Inventory.java and InventoryResource.java under this repo's /inventory folder - Make sure you understand their business logic</br>

You can also populate the Database with values in the src/main/resources/import.sql so we can see some data- You can find some sample data under this repo's /inventory/import.sql (just add the statements to your file)</br>

Install Quarkus plugin to CodeReady Workspace using the CLI:</br>
mvn -q quarkus:add-extension -Dextensions="hibernate-orm-panache, jdbc-h2, smallrye-health" -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory</br>
mvn -q quarkus:add-extension -Dextensions="jdbc-postgresql,openshift" -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory</br>

Add the following variables in src/main/resources/application.properties:</br>

%prod.quarkus.datasource.db-kind=postgresql</br>
%prod.quarkus.datasource.jdbc.url=jdbc:postgresql://inventory-database:5432/inventory</br>
%prod.quarkus.datasource.jdbc.driver=org.postgresql.Driver</br>
%prod.quarkus.datasource.username=inventory</br>
%prod.quarkus.datasource.password=mysecretpassword</br>
%prod.quarkus.datasource.max-size=8</br>
%prod.quarkus.datasource.min-size=2</br>
%prod.quarkus.hibernate-orm.database.generation=drop-and-create</br>
%prod.quarkus.hibernate-orm.sql-load-script=import.sql</br>
%prod.quarkus.hibernate-orm.log.sql=true</br>

%prod.quarkus.kubernetes-client.trust-certs=true</br>
%prod.quarkus.kubernetes.deploy=true</br>
%prod.quarkus.kubernetes.deployment-target=openshift</br>
%prod.quarkus.openshift.build-strategy=docker</br>
%prod.quarkus.openshift.expose=true</br>

Configure your application's DB under src/main/resources/application.properties by adding the following:</br>
%dev.quarkus.datasource.db-kind=h2</br>
%dev.quarkus.datasource.jdbc.url=jdbc:h2:mem:inventory</br>
%dev.quarkus.datasource.jdbc.driver=org.h2.Driver</br>
%dev.quarkus.datasource.username=inventory</br>
%dev.quarkus.datasource.password=mysecretpassword</br>
%dev.quarkus.datasource.max-size=8</br>
%dev.quarkus.datasource.min-size=2</br>
%dev.quarkus.hibernate-orm.database.generation=drop-and-create</br>
%dev.quarkus.hibernate-orm.log.sql=false</br>

Criteria 4: Deplpy your Quarkus Microservice to OpenShift
=====
In this challenge, you will need to deploy your Quarkus Microservcie to Openshift

Hints
=====
a. You can use an ephemeral PostgreSQL as your DB:
Namespace: choose PLEASE ENTER USERID AT TOP OF PAGE-inventory for the first Namespace. Leave the second one as `openshift`</br>

Database Service Name: inventory-database</br>

PostgreSQL Connection Username: inventory</br>

PostgreSQL Connection Password: mysecretpassword</br>

PostgreSQL Database Name: inventory</br>

b. Deploy to OCP</br>
oc project PLEASE ENTER USERID AT TOP OF PAGE-inventory && \</br>
mvn clean package -DskipTests -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory</br>

c. Add some nice labels</br>
oc label dc/inventory-database app.openshift.io/runtime=postgresql --overwrite && \
oc label dc/inventory app.kubernetes.io/part-of=inventory --overwrite && \
oc label dc/inventory-database app.kubernetes.io/part-of=inventory --overwrite && \
oc annotate dc/inventory app.openshift.io/connects-to=inventory-database --overwrite && \
oc annotate dc/inventory app.openshift.io/vcs-uri=https://github.com/RedHat-Middleware-Workshops/cloud-native-workshop-v2m1-labs.git --overwrite && \
oc annotate dc/inventory app.openshift.io/vcs-ref=ocp-4.7 --overwrite </br>

Criteria 5: Deploy to Prod using CI/CD
=====
In this challenge, you need to create a new project and deploy the previous application using Jenkins. Feel free to use the template called Coolstore Monolith using pipelines. You will need to install a Jenkins (ephemeral) too, again using the available templates.</br>

Once installed, you can access the builds under the Builds tab and run the sample pipeline. You can also add an "Approval task" at the end.
Hints
=====
a. Approval Task after the "Run tests in DEV": </br>
            stage ('Approve Go Live') {</br>
              steps {</br>
                timeout(time:30, unit:'MINUTES') {</br>
                  input message:'Go Live in Production (switch to new version)?'</br>
                }</br>
              }</br>
            }</br>
            
Criteria 6: Monitoring 
=====
In this section, you show some monitoring in OpenShift

