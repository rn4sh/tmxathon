# TMXathon

Criteria 1: Migration
=====

For this challenge, you’ll work with an existing Java EE application designed for a retail webshop. The current version of the webshop is a Java EE application built for Oracle Weblogic Application Server. As part of a modernization strategy you’ve decided to move this application to JBoss EAP, containerize it, and run it on a Kubernetes platform with OpenShift. The application is currently hsoted [here](https://github.com/RedHat-Middleware-Workshops/cloud-native-workshop-v2m1-labs.git) . Application in question in under the /monolith folder

To help you with the migration, you can use the [Migration Toolkit for Applications](https://access.redhat.com/documentation/en-us/migration_toolkit_for_applications/5.2/html/introduction_to_the_migration_toolkit_for_applications/what-is-the-toolkit_getting-started-guide) (MTA) IDE Plugin based on CodeReady Workspaces. 


Hints
=====
a. You can access your [CodeReady Workspaces instance]() (CRW) and log in using the username and password you’ve been assigned (e.g. PLEASE ENTER YOUR USERID AT TOP OF PAGE/r3dh4t1!) </br>
b. Make sure to Git clone the complete [Repo](https://github.com/RedHat-Middleware-Workshops/cloud-native-workshop-v2m1-labs.git) to your CRW</br>
c. The MTA tool is located on the left hand side menu - Click on its icon and then on the + sign to create a new configuration </br>
d. The code for the Weblogic application is located under */monolith* - When configuring MTA for analysis, this folder would be your input </br>
e. The Weblogic applcation that needs to migrated to JBoss EAP, version 7 </br>
f. After running the MTA analysis, your migration's issues will be shown as follows:
![image](https://user-images.githubusercontent.com/40291650/136105599-5ed121fa-5b29-41ba-b7b3-4566542a6da4.png)
g. Under the [/migration](https://github.com/rn4sh/tmxathon/tree/main/migration) folder in the current repo, and to save time, you can use the migrated java classes needed (you can simply overwrite yours with those ones)</br>
h. The file *weblogic-ejb-jar.xml* is not longer needed and needs to be deleted </br>
k. The folder *src/main/java/weblogic* is not needed and needs to be deleted</br>
l. Remove the dependency *org.jboss.spec.javax.rmi:jboss-rmi-api_1.0_spec* from your application POM under youc code's *monolith/pom.xml*
m. When you finish fixing the migration issues - make sure you can build without any errors using: *mvn -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith clean package*</br>
n. Re-run and report - if there are no issues found, you're good to go - Keep your analysis for the final presentation

Criteria 2: Deploy your Monolith to OpenShift
=====
For this challenge, you need to deploy your migrated JBoss App to OpenShift

Hints
=====
a. Add an OpenShift Profile to your POM file under *monolith/pom.xml* - an example is provided this repo's [/migration/openshift-profile](https://raw.githubusercontent.com/rn4sh/tmxathon/main/migration/openshift-profile)</br>
b. Login to your OpenShift [Console](https://console.rh-us-east-1.openshift.com/) and create a new Project</br>
c. Under Topology view, your ops team created a template that you can use, it's called *Coolstore Monolith*. It contains your EAP runtime and DB - make sure you use your User ID (userXX)</br>
d. You can deploy from your CodeReady Workspace instance - Make sure you're logged to OpenShift first using the actions, then switch to the project you just created using: oc project <your-project-name> </br>
e. To get a nice topoly view with icons, you can add labels to your deployments - you can use the commands [here](https://github.com/rn4sh/tmxathon/blob/main/migration/labels) </br>
f. Deploy the application - build first, then deploy:</br>
  *mvn clean package -Popenshift -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith*</br>
  *oc start-build coolstore --from-file $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith/deployments/ROOT.war --follow*</br>
  
Criteria 3: Break one Monolith Service: Create a new Microservice Application using [Quarkus](https://quarkus.io/)
=====
For this challenge, you need to implement the *Inventory* project in Quarkus to make it Serverless ready. You can find the scaffold under the */inventory* folder in your application. Note that you will find 2 classes defined, but not implemented: *Inventory.java* and *InventoryResource.java* </br>
![image](https://user-images.githubusercontent.com/40291650/136110070-4864704c-0187-449b-91f5-0a569432e95e.png)

To save you implementation time, you can find the completed *Inventory.java* and *InventoryResource.java* under this repo's [/inventory](https://github.com/rn4sh/tmxathon/tree/main/inventory) folder - Make sure you understand their business logic</br>

You can also populate the Database with values using the scropt in your *src/main/resources/import.sql* so we can run some tests- You can find  sample data under this repo's [import.sql](https://raw.githubusercontent.com/rn4sh/tmxathon/main/inventory/import.sql) (just add the statements to your file)</br>

Install the Quarkus plugins to your CodeReady Workspace using the CLI:</br>
*mvn -q quarkus:add-extension -Dextensions="hibernate-orm-panache, jdbc-h2, smallrye-health" -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory*</br>
*mvn -q quarkus:add-extension -Dextensions="jdbc-postgresql,openshift" -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory*</br>

Configure your *src/main/resources/application.properties* with your application's DB and OpenShift target - you can check the properties in this following [application.properties](https://github.com/rn4sh/tmxathon/blob/main/inventory/application.properties) and simply copy them to your properties file</br>

Criteria 4: Deplop your Quarkus Microservice to OpenShift
=====
In this challenge, you will need to deploy your Quarkus Microservcie to Openshift. Remember that your application is using a PostgreSQL DB.

Hints
=====
a. You can use an ephemeral PostgreSQL as your DB - use the following details:
*Namespace: choose PLEASE ENTER USERID AT TOP OF PAGE-inventory for the first Namespace. Leave the second one as `openshift`</br>

*Database Service Name: inventory-database</br>

*PostgreSQL Connection Username: inventory</br>

*PostgreSQL Connection Password: mysecretpassword</br>

*PostgreSQL Database Name: inventory*</br>

b. Deploy your Inventory Microservice to OpenShift - switch to your working namespace and deploy</br>
*oc project PLEASE ENTER USERID AT TOP OF PAGE-inventory && \</br>
*mvn clean package -DskipTests -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory</br>

c. Add some nice labels to logically group apps and see the icons - You can use the suggestion [here](https://github.com/rn4sh/tmxathon/blob/main/inventory/labels)</br>

Criteria 5: Deploy to Prod using CI/CD
=====
In this challenge, you need to create a new Project and deploy the previous application using Jenkins. This project will act as your PROD environment. Feel free to use the template called *Coolstore Monolith using pipelines* in your developer's catalog. You will also need to install a Jenkins (ephemeral) image, again using the available templates provided by your Operations team.</br>

Once installed, you can access the Jenkins Builds under the *Builds* in your OpenShift Developer's console and run the sample pipeline. You can also add an "Approval task" after the "Run tests in DEV" step. You can refer to the suggestion [here](https://github.com/rn4sh/tmxathon/blob/main/inventory/ApprovalTask) if you're not familiar with Jenkins.
            
Criteria 6: Monitoring 
=====
In this section, you need to Monitor your application's resources usage, including networking metrics. You will need to run at least 2 Prometheus queries. You can refer to the Monitoring documentation [here](https://docs.openshift.com/container-platform/4.8/monitoring/understanding-the-monitoring-stack.html)

