Movieplex on Openshift + Jenkins
================================

This is the Movieplex7 example applications, prepared to be deployed on OpenShift origin with the application template `wildfly-j2ee-application-template.json`.

The included `Jenkinsfile` will allow Jenkins to deploy the application to OpenShift in a Pipeline by using the image to source (I2S) OpenShift functionality.

# Running the application

For information on how to prepare an OpenShift 3 Origin environment, refer to: https://github.com/openshift/origin/blob/master/examples/sample-app/README.md

If you already have a working OpenShift environment just follow these steps. You will need to have the OpenShift CLI tools installed:

* Login to OpenShift
```
    $ oc login --server=<server_url>
```
* Create a new project
```
    $ oc new-project movieplex-application --display-name="MoviePlex WildFly + Jenkins Application Example" --description="This is an example project to demonstrate OpenShift v3 Origin integrated in an Jekins Pipeline"
```
* Upload the application template. You need to be in the root of this repository
```
    $ oc create -f wildfly-j2ee-application-template.json
```
* Create the applications with the uploaded template. 
```
    $ oc new-app wildfly-j2ee-application
```
**NOTE** This can also be done from the web interface by clicking "Add to Project" and selecting the template. It will allow you to change the parameters. 
	
**NOTE** You can also specify your parameters in the command-line by using --param=PUBLIC_URL=test.domain.org, you should at least specify an PUBLIC_URL parameter matching your environment

* If everything went correctly the applications will automatically build and deployed. It will be accessible on the specified PUBLIC_URL

# Deploying with Jenkins

Once the application is created on OpenShift follow these steps to configure Jenkins deployment to OpenShift

* Install and configure the CloudBees OpenShift CLI Plugin
* Create a Username and Password credentials to login to your OpenShift server
* Create a Pipeline Job and paste the `Jenkinsfile` script on the Pipeline Definition

**NOTE** If you are running Jenkins from another OpenShift project, you can use the system account to authenticate the OpenShift CLI, but you need to grand privileges to deploy on the project. You can do so by issuing the following command:

```oc policy add-role-to-user admin system:serviceaccount:<jenkins project name>:default -n <destination project name>```

In this case, for Jenkins running on CJP project would be:

```oc policy add-role-to-user admin system:serviceaccount:cjp:default -n movieplex-application```

by doing this you can omit the oc login step by not specifiying the credentialsId parameters to the wrapper.

