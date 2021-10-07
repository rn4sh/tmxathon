# Welcome to TMXathon!

About
=====

During the TMXathon, you will be working with your team on creating a fully working Production application, using the knowledge you acquired in the first 2 days. At the end of the TMXathon, your application will be judged based on the following criteria:

**Criteria 1: *Migrating Legacy Apps to OpenShift***</br>
**Criteria 2: *Deploying your migrated Monolith to OpenShift***</br>
**Criteria 3: *Breaking your Monolith into Microservices***</br>
**Criteria 4: *Deploying your Microservice to OpenShift***</br>
**Criteria 5: *Automating Deployment to Prod using Pipelines***</br>
**Criteria 6: *Monitoring using Prometheus Queries and Metrics***</br>

Bonus points will be awarded for:

***Including at least one Serverless application***</br>
***Monitoring your application using a Service mesh***</br>
***Advanced deployment techniques, including pushing your containers to an external registry***</br>
***Restricting your apps CPU and Memory resources***</br>
***Create your own Identity provider, or simply connect to an existing one***</br>
***Include Event Driven Architecture components***</br>


Agenda
=====
| Day        | Time           | Activity    |       
| ------------- | ------------- | ------------- |
| Monday      | 10 a.m-5 p.m| Enablement 1: Service Mesh|
| Tuesday      | 10 a.m-5 p.m| Enablement 2: Serverless|
| Wednesday      | 10 a.m-11 a.m| Hackaton Kickoff|
|       | 11 a.m-5 p.m| Hackaton Preparation: Review Day 1-2 modules and [http://learn.openshift.com](http://learn.openshift.com)|
| Thursday      | 10 a.m-5 p.m| Hackaton Day 1|
| Friday      | 10 a.m-2 p.m| Hackaton Day 2|
|       | 2 p.m-4 p.m| Project submission and presentation|
|       | 5 p.m| Announcing Winners|


Criteria 1: Migrating Legacy Apps to OpenShift
=====

For this challenge, you’ll work with an existing Java EE application designed for a retail webshop. The current version of the webshop is a Java EE application built for Oracle Weblogic Application Server. As part of a modernization strategy you’ve decided to move this application to JBoss EAP, containerize it, and run it on a Kubernetes platform with OpenShift. The application is currently hosted [here](https://github.com/RedHat-Middleware-Workshops/cloud-native-workshop-v2m1-labs.git) . Application in question in under the /monolith folder

To help you with the migration, you can use the [Migration Toolkit for Applications](https://developers.redhat.com/products/mta/use-cases) (MTA) IDE Plugin based on CodeReady Workspaces. 

Hints
=====
a. You can access your [CodeReady Workspaces instance](https://codeready-labs-infra.apps.cluster-4k8mv.4k8mv.sandbox1663.opentlc.com/) (CRW) and log in using the username and password you’ve been assigned - Make sure you delete any previous projects you might have to avoid conflicts </br>
b. Make sure to Git clone the complete [Repo](https://github.com/RedHat-Middleware-Workshops/cloud-native-workshop-v2m1-labs.git) to your CRW</br>
c. The MTA tool is located on the left hand side menu - Click on its icon and then on the + sign to create a new configuration </br>
d. The code for the Weblogic application is located under */monolith* - When configuring MTA for analysis, this folder would be your --input - Make sure you add weblogic as your --source </br>
e. The Weblogic application that needs to migrated to JBoss EAP, version 7 </br>
f. After running the MTA analysis, your migration's issues will be shown as follows:
![image](https://user-images.githubusercontent.com/40291650/136105599-5ed121fa-5b29-41ba-b7b3-4566542a6da4.png)
g. Under the [/migration](https://github.com/rn4sh/tmxathon/tree/main/migration) folder in the current repo, and to save time, you can use the migrated java classes needed (you can simply overwrite yours with those ones)</br>
h. The file *weblogic-ejb-jar.xml* is not longer needed and needs to be deleted </br>
k. The folder *src/main/java/weblogic* is not needed and needs to be deleted</br>
l. Remove the dependency *jboss-rmi-api_1.0_spec* from your application POM under your application's *monolith/pom.xml*
m. When you finish fixing the migration issues - make sure you can build without any errors using: *mvn -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith clean package*</br>
n. Re-run and report - if there are no issues found, you're good to go - Keep your analysis for the final presentation

Criteria 2: Deploy your migrated Monolith to OpenShift
=====
For this challenge, you need to deploy your migrated JBoss App to OpenShift

Hints
=====
a. Add an OpenShift Profile to your POM file under *monolith/pom.xml* - an example is provided this repo's [/migration/openshift-profile](https://raw.githubusercontent.com/rn4sh/tmxathon/main/migration/openshift-profile)</br>
b. Login to your OpenShift [Console](https://console-openshift-console.apps.cluster-4k8mv.4k8mv.sandbox1663.opentlc.com/) and create a new Project - call it **userXX-coolstore-dev**(userXX is your assigned UserID)</br>
c. Under Topology view, your ops team created a template that you can use, it's called *Coolstore Monolith*. It contains your EAP runtime and DB - make sure you use your User ID (userXX) - You find it under the "From Catalog" option</br>
d. Deploy your source code from your CodeReady Workspace instance - Make sure you're logged to OpenShift first using the actions, then switch to the project you just created using: oc project <your-project-name> </br>
e. To get a nice topology view with icons, you can add labels to your deployments - you can use the commands [here](https://github.com/rn4sh/tmxathon/blob/main/migration/labels) </br>
f. Deploy the application - build first, then deploy:</br>
*mvn clean package -Popenshift -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith* </br>
*oc start-build coolstore --from-file $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith/deployments/ROOT.war --follow* </br>
  
Criteria 3: Start Breaking your Monolith: Create a new Microservice Application using [Quarkus](https://quarkus.io/)
=====
For this challenge, you need to implement the *Inventory* project in Quarkus to make it Cloud-native and Serverless ready. You can find the scaffold under the */inventory* folder in your application. Note that you will find 2 classes defined, but not implemented: *Inventory.java* and *InventoryResource.java* </br>
![image](https://user-images.githubusercontent.com/40291650/136110070-4864704c-0187-449b-91f5-0a569432e95e.png)

To save you some implementation time, you can find the completed *Inventory.java* and *InventoryResource.java* under this repo's [/inventory](https://github.com/rn4sh/tmxathon/tree/main/inventory) folder - Make sure you understand their business logic</br>

You can also populate the Database with values using the script in your *src/main/resources/import.sql* so we can run some tests- You can find sample data under this repo's [import.sql](https://raw.githubusercontent.com/rn4sh/tmxathon/main/inventory/import.sql) (just add the statements to your sql file)</br>

Install the Quarkus plugins to your CodeReady Workspace using the CLI:</br>
*mvn -q quarkus:add-extension -Dextensions="hibernate-orm-panache, jdbc-h2, smallrye-health" -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory*</br>
*mvn -q quarkus:add-extension -Dextensions="jdbc-postgresql,openshift" -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory*</br>

Configure your *src/main/resources/application.properties* with your application's DB and OpenShift target - you can check the properties in this following [application.properties](https://github.com/rn4sh/tmxathon/blob/main/inventory/application.properties) and simply copy them to your properties file</br>

Criteria 4: Deploy your Microservice to OpenShift
=====
For this challenge, you will need to deploy your Quarkus Microservice to Openshift. Remember that your application is using a PostgreSQL DB.

Hints
=====
a. You can use an ephemeral PostgreSQL as your DB - use the following details:
*Namespace: choose userXX-coolstore-dev for the first Namespace. Leave the second one as `openshift`*</br>
*Database Service Name: inventory-database*</br>
*PostgreSQL Connection Username: inventory*</br>
*PostgreSQL Connection Password: mysecretpassword*</br>
*PostgreSQL Database Name: inventory*</br>

b. Deploy your Inventory Microservice to OpenShift - switch to your working namespace first and deploy</br>
*oc project your_project_name*</br>
*mvn clean package -DskipTests -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/inventory*</br>

c. Add some nice labels to logically group apps and see the icons - You can use the suggestion [here](https://github.com/rn4sh/tmxathon/blob/main/inventory/labels)

Criteria 5: Deploy to Prod using CI/CD
=====
For this challenge, you need to create a new Project, call it **userXX-coolstore-prod** (userXX is your assigned UserID), and deploy the previous application using Jenkins. This project will act as your PROD environment. Feel free to use the template called *Coolstore Monolith using pipelines* in your developer's catalog. You will also need to install a Jenkins (ephemeral) image, again using the available templates provided by your Operations team.</br>

Once installed, you can access the Jenkins Builds under the *Builds* in your OpenShift Developer's console and run the sample pipeline. You can also add an "Approval task" after the "Run tests in DEV" step. You can refer to the suggestion [here](https://github.com/rn4sh/tmxathon/blob/main/inventory/ApprovalTask) if you're not familiar with Jenkins.
            
Criteria 6: Monitoring
=====
For this challenge, you need to Monitor your application's resources usage, including networking metrics. You will need to run at least 2 Prometheus queries. You can refer to the Monitoring documentation [here](https://docs.openshift.com/container-platform/4.8/monitoring/understanding-the-monitoring-stack.html) - If you're not a Cluster admin, you might need to ask for additional priviliges.

