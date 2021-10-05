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

Criteria 2: Deploy to OpenShift
=====
For this challenge, you deploy your migrated JBoss App to OpenShift

Hints
=====
a. Add an OpenShift Profile to your POM file under monolith/pom.xml - a profile example can be found under this repo's /migration folder</br>
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
f. Deploy the application - build first, then deploy:
  mvn clean package -Popenshift -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith
  oc start-build coolstore --from-file $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m1-labs/monolith/deployments/ROOT.war --follow</br>

  
