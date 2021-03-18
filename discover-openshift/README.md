# Discover OpenShift

## Table of Content
* [Explore OpenShift](#explore-openShift)
  * [Webconsole](#webconsole)
  * [Command Line Interface](#command-line-interface)
  * [Projects & Perspectives](#projects-&-perspectives)
* [Deploying Components](#deploying-components)
  * [Deploying a Database](#deploying-a-database)
* [Building Containers](#building-containers)
  * [Using Catalog Templates](#using-catalog-templates)
  * [Using the OC CLI](#using-the-oc-cli)
  * [Binary builds with ODO CLI](#binary-builds-with-odo-cli)
* [Application Healthchecks](#application-healthchecks)
  * [From the Webconsole](#from-the-webconsole)
  * [From the CLI](#from-the-cli)
* [Application Configuration & Data](#application-configuration-&-data)
  * [ConfigMap and Secrets](#configmap-and-secrets)
  * [Persistent Volumes](#persistent-volumes)
* [Continuous Integration and Pipelines](#continuous-integration-and-pipelines)
  * [CI/CD with Jenkins](#ci-cd-with-jenkins)
  * [CI/CD with OpenShift Pipelines](#ci-cd-with-openshiftpipelines)
* [Monitoring](#monitoring)
  * [Built-in monitoring](#built-in-monitoring)
  * [Prometheus & ServiceMonitor](#prometheus-&-servicemonitor)
  * [Grafana dashboarding](#grafana-dashboarding)
* [Useful CLI commands](#useful-cli-commands)

## Explore OpenShift

### Webconsole

OpenShift ships with a web-based console that will allow users to perform various tasks via a browser.

The first time you access the web console, you will most likely be in the Administrator perspective. If you are also the Cluster Administrator, you will be presented with an overview of the whole Cluster status:

![webconsole_overview.png](./assets/webconsole_overview.png)

### Command Line Interface

OpenShift ships also a command line tools to provide users with a nice interface to work with applications deployed to the platform. The OpenShift tools are a single executable written in the Go programming language and is available for the following operating systems:

* Microsoft Windows
* Mac OS X
* Linux

```sh
$ oc version
Client Version: openshift-clients-4.3-31-gc25fb9c7
Server Version: 4.6.4
Kubernetes Version: v1.19.0+9f84db3
```

If the `oc` doesn’t exist or you have an older version of the OpenShift CLI, follow the next sections to install or update the OpenShift CLI.

#### Download CLI

From top right menu bar, click to the little white question mark icon, then click to **Command Line Tools**:

![webconsole_cli_links.png](./assets/webconsole_cli_links.png)

Scroll down to **oc - OpenShift Command Line Interface (CLI)**

![webconsole_cli_binaries.png](./assets/webconsole_cli_binaries.png)

Choose the binary corresponding to your platform. If needed, check the installation details below:

* [Download and Install OpenShift CLI on Linux](https://redhat-scholars.github.io/openshift-starter-guides/rhs-openshift-starter-guides/common-explore.html#download_and_install_openshift_cli_on_linux)
* [Download and Install OpenShift CLI on Mac OS X](https://redhat-scholars.github.io/openshift-starter-guides/rhs-openshift-starter-guides/common-explore.html#download_and_install_openshift_cli_on_mac)
* [Download and Install OpenShift CLI on Windows](./assets/https://redhat-scholars.github.io/openshift-starter-guides/rhs-openshift-starter-guides/common-explore.html#download_and_install_openshift_cli_on_windows)

#### Connect to cluster with CLI

Once your `oc` client is setup on your Workstation, you can connect to cluster and start working also with OpenShift CLI.

From Web Console overview, go to top-right menu bar and click to the dropdown menu containing your username, then click **Copy Login Command**:

![webconsole_copy_login_command.png](./assets/webconsole_copy_login_command.png)

Click on **Display Token** and copy the command under **Login with this token**:

```sh
$ oc login --token=some_token --server=https://c104-e.us-east.containers.cloud.ibm.com:32208

Logged into "https://c104-e.us-east.containers.cloud.ibm.com:32208" as "%USERNAME%" using the token provided.

You have access to 68 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```

### Projects & Perspectives

Projects are a top level concept to help you organize your deployments. An OpenShift project allows a community of users (or a user) to organize and manage their content in isolation from other communities. Each project has its own resources, policies (who can or cannot perform actions), and constraints (quotas and limits on resources, etc). Projects act as a "wrapper" around all the application services and endpoints you (or your teams) are using for your work.

#### Switch Perspectives

At the top of the left navigation menu, you can toggle between the **Administrator** perspective and the **Developer** perspective.

![projects_perspectives.png](./assets/projects_perspectives.png)

Select **Developer** to switch to the Developer perspective. Once the Developer perspective loads, you should be in the **Topology** view.  When starting a Project, there are no applications or components to view.

![projects_perspectives_topology.png](./assets/projects_perspectives_topology.png)

#### Create a Project

You can create a project from either **Administrator** or **Developer** perspective. Just insert the name of your project in the form and click **Create**.

![projects_creation](./assets/projects_creation.png)

#### Using the CLI

You can also create and manage project from the CLI as well. Let’s use our newly created project workshop as our default one:

```sh
$ oc new-project my-own-project

$ oc project my-other-project
Now using project "my-other-project" on server "https://c104-e.us-east.containers.cloud.ibm.com:32208"
```

## Deploying Components

Once your project is created, it may be the time to add some components to it :wink: On the **Developer** perspective, you may just use the **Topology** view or the **+Add** menu on the left to add new components **From Catalog**. Once this option picked, you'll have access to the **Developer Catalog**:

![catalog_developer.png](./assets/catalog_developer.png)

Depending on your cluster installation (and the administrator willingness to offer access to services) you'll be able to consumer different kind of middleware or application services. If you need extra-services that may not be present, please have a look at [OperatorHub.io](https://operatorhub.io) that is a community marketplace for additional Kubernetes Services packaged as [Operators](https://www.redhat.com/en/topics/containers/what-is-a-kubernetes-operato).

> Maybe some of those Operators are already present on your cluster but not yet available for your project? To check this go back to the **Administrator** perspective and select **OperatorHub** in the **Operators** menu item. You may be able to activate operators for bringing new services into your project.

![catalog_operatorhub](./assets/catalog_operatorhub.png)

### Deploying a Database

Most useful applications are "stateful" or "dynamic" in some way, and this is usually achieved with a database or other data storage.

In the Developer Perspective in your project, click **+Add** and then **Databases**. You'll just see the default available databases on OpenShift in the below screenshot:

![catalog_databases](./assets/catalog_databases.png)

> Make sure to uncheck **Operator Backed** option from **Type** section

Let's deploy for example a **MongoDB** database! Here we are using a template - a preconfigured set of resources that includes parameters that can be customized. In our case, the parameters we are concerned with are — user, password, database, and admin password.

![catalog_mongodb](./assets/catalog_mongodb.png)

You can see that some of the fields say **"generated if empty"**. This is a feature of **Templates** in OpenShift. Just click **Create** and OpenShift will instantiate a containerized database for you.

If you did not explicitly set the username and password, you can retrieve the generated ones that have been stored into a `Secret`. Just go to the **Secrets** left menu and look after a secret having the same name as your database service. You can reveal the values to get actual username and password.

![catalog_mongodb_secret](./assets/catalog_mongodb_secret.png)

> Though we covered here the MongDB example, the process, parameters and secrets remain the same for other database engines like MariaDB or PostgreSQL.

## Building Containers

In this section, we will expand on that by learning how OpenShift builds container images using source code from an existing repository. This is accomplished using the Source-to-Image project.

[Source-to-Image (S2I)](https://github.com/openshift/source-to-image) is a open source project sponsored by Red Hat that has the following goal:

```
Source-to-image (S2I) is a tool for building reproducible container images. S2I
produces ready-to-run images by injecting source code into a container image and
assembling a new container image which incorporates the builder image and built
source. The result is then ready to use with docker run. S2I supports
incremental builds which re-use previously downloaded dependencies, previously
built artifacts, etc.
```

OpenShift is S2I-enabled and can use S2I as one of its build mechanisms (in addition to building container images from Dockerfiles, and "custom" builds).

OpenShift runs the S2I process inside a special **Pod**, called a Build Pod, and thus builds are subject to quotas, limits, resource scheduling, and other aspects of OpenShift.

### Using Catalog Templates

Similar to how we used **+Add** before with an existing database template, we can do the same for specifying a source code repository. In the Developer Perspective, click +Add in the left navigation and then choose "From Git" let’s use it with a simple Java S2I image.

OpenShift can work with any accessible Git repository. This could be GitHub, GitLab, or any other server that speaks Git. You can even register webhooks in your Git server to initiate OpenShift builds triggered by any update to the application code!

The **Import from Git** workflow will guide you through the process of deploying your app based on a few selections.

![build_catalog_java](./assets/build_catalog_java.png)

You'll be able to put additional `labels` to the Deployment you will create. Labels help classifying your objects in a project and cluster. By default, it will also create a network **Route** for accessing the application from outside of the cluster.

Once you click **Create**, you'll go back to the **Topology** view and would be able to inspect the created resources:

![build_catalog_java_resources](./assets/build_catalog_java_resources.png)

You can than access the build logs by clicking on **View Logs** in the **Builds** section of the **Resources** tab.

![build_catalog_java_logs](./assets/build_catalog_java_logs.png)

Wait a few minutes and you'll have an application up-and-running.

### Using the OC CLI

The task we've done so far using the **Import from Git** workflow in the webconsole can also be fully realized using the CLI. Useful when you want to make the things repeatable!

```sh
$ oc new-app nodejs~https://github.com/redhat-france-sa/openshift-by-example.git --context-dir=/frontend --name frontend --labels='app.kubernetes.io/part-of=fruits-catalog,app.openshift.io/runtime=nodejs,version=v1'

--> Found image e86504c (2 weeks old) in image stream "openshift/nodejs" under tag "10-SCL" for "nodejs"
 
    Node.js 10 
    ---------- 
    Node.js 10 available as container is a base platform for building and running various Node.js 10 applications and frameworks. Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.
 
    Tags: builder, nodejs, nodejs10
 
    * A source build using source code from https://github.com/redhat-france-sa/openshift-by-example.git will be created
    * The resulting image will be pushed to image stream tag "frontend:latest"
    * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "frontend"
    * Port 8080/tcp will be load balanced by service "frontend"
    * Other containers can access this service through the hostname "frontend"
 
--> Creating resources with label app.kubernetes.io/part-of=fruits-catalog,app.openshift.io/runtime=nodejs ...
    imagestream.image.openshift.io "frontend" created
    buildconfig.build.openshift.io "frontend" created
    deploymentconfig.apps.openshift.io "frontend" created
    service "frontend" created
--> Success
    Build scheduled, use 'oc logs -f bc/frontend' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/frontend' 
    Run 'oc status' to view your app.
```

To follow the running builds through the CLI, you can use:

```sh
$ oc get builds
$ oc logs -f $BUILD_POD_NAME
```

> Whilst we address NodeJS with the previous command, same commands exist for different language. To check which **builder image** are available, you can run the `oc new-app --list` command (or the most complete one `oc get is -n openshift -o json |jq '.items[]|select(.spec.tags[].annotations.tags|contains("builder")?).metadata.name' |sort |uniq` :wink: )

Depending the language, it will or it will not create automatically a network **Route** to access your application for the outside of the cluster. For NodeJS, for example, you'll have to create it explicitly using the following command:

```sh
$ oc expose svc/frontend
route.route.openshift.io/frontend exposed
```

### Binary builds with ODO CLI

`odo` is a CLI tool for creating applications on OpenShift Container Platform and Kubernetes. With `odo`, you can write, build, and debug applications on a cluster without the need to administer the cluster itself. Creating deployment configurations, build configurations, service routes and other OpenShift Container Platform or Kubernetes elements are all automated by `odo`.

It may be interesting to consider `odo` for NodeJS or Java development as `odo` allows you to realize **Binary builds**. S2I is a great way to get from source code to a container, but the process is a bit too slow for daily fast iterative development. With ODO you're reusing the applications dependencies and binaries that are already present on the developer machine.

Like `oc`, `odo` can also be downloaded from the top right menu bar, clicking on the little white question mark icon, then picking **Command Line Tools**.

```sh
$ odo version
odo v1.1.2 (f7fcb5396)

Server: https://api.cluster-3a96.3a96.example.opentlc.com:6443
Kubernetes: v1.19.0+9f84db3
```

Here's below how to create a ODO Java component called `backend` just from the component directory - we expect here that the target binary jar as already been produced using Maven or some other build system.

```sh
$ odo create java:8 backend --app fruits-catalog --binary target/fruits-catalog-1.0.0-SNAPSHOT.jar --port 8080/tcp
Validation
 ✓  Validating component [41ms]
 
Please use `odo push` command to create the component with source deployed
```

Once component is create, you just have to push it to the cluster:

```sh
$ odo push
Validation
 ✓  Checking component [67ms]
 
Configuration changes
 ✓  Initializing component
 ✓  Creating component [215ms]
 
Pushing to component backend of type binary
 ✓  Checking files for pushing [2ms]
 ✓  Waiting for component to start [24s] 
 ✓  Syncing files to the component [11s]
 ✓  Building component [2s]
 ```

 The `odo watch` command is also very helpful for watching the binary or a directory for changes and automatically pushing the changes to the running container within the cluster!

## Application Healthchecks

There is a concept of application health checks in OpenShift. Health checks are important to help the platform determine if your application is correctly running or if it need to be restarted. As an example, if you plan to implement a realistic CI/CD pipeline, you will be doing some testing of the "development" version of the application. However, in order to test the app, it must be ready. This is where OpenShift’s application health features come in very handy.

Health checks come in two flavors:

* Readiness probe
* Liveness probe

From the [Application Health](https://docs.openshift.com/container-platform/4.5/applications/application-health.html) section of the documentation, we see the definitions:

**Liveness Probe**
> A liveness probe checks if the container in which it is configured is still running. If the liveness probe fails, the kubelet kills the container, which will be subjected to its restart policy. Set a liveness check by configuring the `template.spec.containers.livenessprobe` stanza of a pod configuration.

**Readiness Probe**
> A readiness probe determines if a container is ready to service requests. If the readiness probe fails a container, the endpoints controller ensures the container has its IP address removed from the endpoints of all services. A readiness probe can be used to signal to the endpoints controller that even though a container is running, it should not receive any traffic from a proxy. Set a readiness check by configuring the `template.spec.containers.readinessprobe` stanza of a pod configuration.

### From the Webconsole

From the **Topology** view, click your component. On the side panel, click the **Actions** dropdown menu and the select **Add Health Checks**.

![healthchecks-menu](./assets/healthchecks-menu.png)

The new form allows you to declare a **Command**, a **Socket** presence or a **HTTP Endpoint** as a valid probe for your component:

![healthchecks-settings](./assets/healthchecks-settings.png)

### From the CLI

The same kind of operation is also available from the commande line to make things repeatable:

```sh
oc set probe dc/nginx --readiness --get-url=http://:8080/healthz --initial-delay-seconds=10
oc set probe dc/nginx --liveness --get-url=http://:8080/healthz --initial-delay-seconds=10
```

## Application Configuration & Data

### ConfigMap and Secrets

A [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/) allows you to decouple environment-specific configuration from your container images, so that your applications are easily portable.

`ConfigMap` is used to store non-confidential data in key-value pairs. Application Pods can then consume ConfigMaps as environment variable or as configuration file mounted in a volume.

You can easily define a ConfigMap using a YAML fragment like below:

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: backend-config
  labels:
    app: fruits-catalog
    deployment: backend
data:
  application.yml: |-
    spring.data.mongodb.uri: mongodb://${MONGODB_DATABASE_USER}:${MONGODB_DATABASE_PASSWORD}@mongodb:27017/sampledb
```

And then create/register it within your project with the following CLI command:

```sh
oc create -f backend-config.yaml
```

This ConfigMap can then be mounted by a deployment as a read-only volume:

```sh
oc set volume dc/backend-fruits-catalog --add --name=backend-config-volume  --type=configmap --configmap-name=backend-config --mount-path=/deployments/config
```

or injected into the deployment as environment variables using a specific prefix:

```sh
oc set env dc/backend-fruits-catalog --from=configmap/ackend-config --prefix=CONFIG_
```

> [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) are following the same approach but are dedicated to sensitive data. Their content is encoded once created and decoded when injected into the using Pod.

### Persistent Volumes

A `PersistentVolume` (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. 

Most of the time, Persistent Volume are created once you're instantiating components through the Developer Catalog.

As a end-user you'll probably just end up creating and managing [`PersistentVolumeClaims`](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) (PVC) and then attach this claim as a volume of specific deployment.

Create a PVC like below:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nexus
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
```

and then set the resulting PV as a volume of a deployment:

```sh
oc set volume dc/nexus --add --name=nexus-storage -t pvc --claim-name=nexus --claim-mode=ReadWriteMany --claim-size=1Gi --mount-path=/sonatype-work
```

## Continuous Integration and Pipelines

A continuous integration and delivery (CI/CD) pipeline is an automated expression of your process for getting software from version control right through to your users and customers. Every change to your software (committed in source control) goes through a complex process on its way to being released. This process involves building the software in a reliable and repeatable manner, as well as progressing the built software (called a "build") through multiple stages of testing and deployment.

OpenShift ships two different CI/CD engines: Jenkins and OpenShift Pipelines.

### CI/CD with Jenkins

OpenShift Container Platform provides a container image for running [Jenkins](https://www.jenkins.io/). This image provides a Jenkins server instance, which can be used to set up a basic flow for continuous testing, integration, and delivery.

From the Developer Catalog in the **Developer** perspective, choose the **CI/CD** category to access the Jenkins related templates:

![cicd_jenkins_install.png](./assets/cicd_jenkins_install.png)

The instantiation of the templates is straightforward and default values are usually great.

After having deployed your Jenkins instance, you will have to setup some RBAC permissions regarding the service account that is running Jenkins. Depending on the projects your Jenkins instance should be able to edit, or the projects that should be able to pull images from, the below commands should be adapted:

```sh
oc adm policy add-role-to-user edit system:serviceaccount:fruits-catalog-dev:jenkins -n fruits-catalog-prod
oc adm policy add-role-to-group system:image-puller system:serviceaccounts:fruits-catalog-prod -n fruits-catalog-dev
```

* The 1st tells that the `jenkins` account located into the `fruits-catalog-dev` project will be able to modify elements from the `fruits-catalog-prod` project. Typically, the deployment configuration to rollout a release,
* The 2nd one tells that all the service accounts from `fruits-catalog-prod` should be able to access container images that have been built and stored within the `fruits-catalog-dev` project.

#### Writing a Pipeline

A Jenkins pipeline can be wrapped into a `BuildConfig` object that has the `JenkinsPipeline` strategy as shown below. More details can be found in [official documentation](https://docs.openshift.com/container-platform/4.6/builds/build-strategies.html#builds-strategy-pipeline-build_build-strategies).

```yaml
kind: BuildConfig
apiVersion: build.openshift.io/v1
metadata:
  name: fruits-cicd-pipeline
spec:
  triggers:
    - type: GitHub
      github:
        secret: secret101
    - type: Generic
      generic:
        secret: secret101
  nodeSelector: {}
  output: {}
  resources: {}
  successfulBuildsHistoryLimit: 5
  failedBuildsHistoryLimit: 5
  strategy:
    type: JenkinsPipeline
    jenkinsPipelineStrategy:
      jenkinsfile: >-

        // NOTE, the "pipeline" directive/closure from the declarative pipeline
        syntax needs to include, or be nested outside,

        // and "openshift" directive/closure from the OpenShift Client Plugin
        for Jenkins.  Otherwise, the declarative pipeline engine

        // will not be fully engaged.

        pipeline {
            agent {
              node {
                // This will speed up the start of the pipeline, instead of using a dedicated nodejs slave
                label 'master'
              }
            }
            options {
                // set a timeout of 40 minutes for this pipeline
                timeout(time: 40, unit: 'MINUTES')
            }

            stages {
                stage('Build') {
                    steps {
                        script {
                            openshift.withCluster() {
                                openshift.withProject() {
                                  parallel(
                                     frontend: {
                                        openshift.startBuild("frontend").logs('-f')

                                      },
                                      backend: {
                                        openshift.startBuild("backend").logs('-f')
                                      }
                                    )
                                }
                            }
                        } // script
                    } // steps
                } // stage
                
                stage('deploy to DEV') {
                    steps {
                        script {
                            openshift.withCluster() {
                                openshift.withProject() {
                                    // dcs will contain our 2 deploymentConfigs
                                    def dcs = openshift.selector( [ 'dc/frontend', 'dc/backend-fruits-catalog'])

                                    // We want to make sure that each pod is 'Running' before moving forward
                                    dcs.withEach {
                                      it.related('pods').untilEach(1) {
                                        return (it.object().status.phase == "Running")
                                      }
                                    }
                                }
                            }
                        } // script
                    } // steps
                } // stage
                stage('deploy to PROD') {
                    steps {
                        script {
                            openshift.withCluster() {
                                openshift.withProject() {
                                  //By tagging the images with "promoteToProd, OpenShift will deploy these versions into PROD environment"
                                  openshift.tag("fruits-catalog-dev/frontend:latest", "fruits-catalog-dev/frontend:promoteToProd")
                                  openshift.tag("fruits-catalog-dev/backend:latest", "fruits-catalog-dev/backend:promoteToProd")
                                }
                            }
                        } // script
                    } // steps
                } // stage
                
            } // stages
        } // pipeline
  postCommit: {}
  source:
    type: None
  triggers: []
  runPolicy: Serial
```

The pipeline above should be created within the project hosting the Jenkins instance using the following command-line:

```sh
oc create -f fruits-cicd-pipeline.yaml
```

This pipeline is a regular Jenkins pipeline but is using some OpenShift Jenkins client plugin functions. The full reference of available functions can be found [here](https://github.com/openshift/jenkins-client-plugin).

#### Executing a Pipeline

Once registered within your project, Pipelines are accessible through the **Builds** menu of the **Developer** perspective. 

A pipeline can be started simply by using the **Actions** menu on the top right and then selecting the **Start Build** menu item.

![cicd_jenkins_start](./assets/cicd_jenkins_start.png)

The same command can also be executed using the `oc` command line:

```sh
oc start-build fruits-cicd-pipeline -n fruits-catalog-dev
```

Another way to start a Pipeline is by automatically triggering it using a Webhook. Webhook URLs are automatically generated by OpenShift during the pipeline creation and can be retrieved from the Pipeline details page on the webconsole:

![cicd_jenkins_triggers](./assets/cicd_jenkins_triggers.png)

They can also be retrieved using the `oc` command line utility as shown below:

```sh
$ oc describe bc fruits-cicd-pipeline -n fruits-catalog-dev
[...]
Triggered by:		<none>
Webhook GitHub:
	URL:	https://api.cluster-3a96.3a96.example.opentlc.com:6443/apis/build.openshift.io/v1/namespaces/fruits-catalog-dev/buildconfigs/fruits-cicd-pipeline/webhooks/<secret>/github
Webhook Generic:
	URL:		https://api.cluster-3a96.3a96.example.opentlc.com:6443/apis/build.openshift.io/v1/namespaces/fruits-catalog-dev/buildconfigs/fruits-cicd-pipeline/webhooks/<secret>/generic
	AllowEnv:	false
Builds History Limit:
	Successful:	5
	Failed:		5

Build			Status		Duration		Creation Time
fruits-cicd-pipeline-2 	running 	running for 7m34s 	2021-03-18 14:36:37 +0100 CET
fruits-cicd-pipeline-1 	complete 	4m15s 			2021-03-18 10:58:24 +0100 CET
```

The different pipeline runs (called `Build` where the pipeline definition is a `BuildConfig`) can be inspected using the webconsole:

![cicd_jenkins_run](./assets/cicd_jenkins_run.png)

### CI/CD with OpenShift Pipelines

OpenShift Pipelines is a cloud-native, continuous integration and delivery (CI/CD) solution for building pipelines using [Tekton](https://tekton.dev/). Tekton is a flexible, Kubernetes-native, open-source CI/CD framework that enables automating deployments across multiple platforms (Kubernetes, serverless, VMs, etc) by abstracting away the underlying details.

A full and comprehensive guide on **OpenShift Pipeline** is available [here](https://redhat-scholars.github.io/openshift-starter-guides/rhs-openshift-starter-guides/nationalparks-java-pipeline.html).

## Monitoring

### Built-in monitoring

OpenShift offers built-in automatic monitoring for each and every container deployed on the platform. Using the **Monitoring** menu from the **Developer** perspective you can access standard metrics corresponding to the resources consumed by your components: CPU, Memory, Storage and Network bandwidth.

![monitoring_builtin](./assets/monitoring_builtin.png)

The built-in monitoring can be considered as infrastructure level monitoring of your component.

### Prometheus & ServiceMonitor

OpenShift allows to easily integrate custom application metrics by defining a new monitoring layer. For that, it is using the [Prometheus](https://prometheus.io/) metrics collector and storage engine.

Metrics collection can be integrated in a *as-code* fashion using Prometheus Operator and the `ServiceMonitor` concept. See [project official documentation](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/user-guides/getting-started.md).

In order to activate these features, you first have to ensure that the Prometheus Operator is installed and enabled within your project. If this is not the case, switch to the **Administrator** perspective and use the **OperatorHub** menu to install the *Prometheus Operator* as shown below:

![monitoring_prometheus_op_install](./assets/monitoring_prometheus_op_install.png)

Once the operator installed, you'll have to deploy an instance of `Prometheus` using the **Developer Catalog**. 

![monitoring_prometheus_install](./assets/monitoring_prometheus_install.png)

Default parameters of the Prometheus installation are great, just hit the **Create** button then.

Now we should be able to defined our `ServiceMonitor` that is a object holding some scraping rules for Prometheus. Is the example below, we are declaring a new endpoint to scrape and integrate into the Prometheus engine each and every 5 seconds:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
 labels:
   k8s-app: prometheus
 name: mymonitor
 namespace: fruits-catalog-prod
spec:
 endpoints:
   - interval: 5s
     path: /actuator/prometheus
     port: 8080-tcp
 selector:
   matchLabels:
     app.kubernetes.io/instance: backend
```

Creating a `ServiceMonitor` is enough for Prometheus to start gathering and storing metrics.

### Grafana dashboarding

[Grafana](https://grafana.com/) is another tool that is integrated with OpenShift and Prometheus in order to provider visualization of the metrics stored into Prometheus.

The usage of Grafana is following the same principle as Prometheus. We first have to make sure that the Grafana Operator is installed and enabled within your project. If this is not the case, switch to the **Administrator** perspective and use the **OperatorHub** menu to install the *Grafana Operator* as shown below:

![monitoring_grafana_op_install](./assets/monitoring_grafana_op_install.png)

Then, once installed, you'll have to deploy a `Grafana` instance using the **Developer Catalog** from the **Developer** perspective like illustrated below:

![monitoring_grafana_install](./assets/monitoring_grafana_install.png)

> We recommend setting an administrator username and password if you want to use Grafana in interactive mode.

In order to link our new Grafana instance to the previous Prometheus one, we have to defined a `GrafanaDataSource` so that the metrics collecte by Prometheus will be available for visualization through Grafana:

```yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDataSource
metadata:
  name: fruits-grafanadatasource
spec:
  name: middleware.yaml
  datasources:
    - name: Prometheus
      type: prometheus
      access: proxy
      url: http://prometheus-operated:9090
      isDefault: true
      version: 1
      editable: true
      jsonData:
        tlsSkipVerify: true
        timeInterval: "5s"
```

From here, you can just start using Grafana in interactive mode, crafting new dashboard after having sign-in. Or - if you already have ready to use dashboards - you can just insert them within your instance by declaring them within a `GrafanaDashboard` object like below. Just create this object in your project and the Grafana Operator will take care of importing it into the instance!

```yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDashboard
metadata:
  name: fruits-monitoring-dashboard
  labels:
    app: grafana
spec:
  name: fruits-dashboard.json
  json: >
    {
      "__inputs": [
        {
          "name": "DS_PROMETHEUS",
          "label": "Prometheus",
          "description": "",
          "type": "datasource",
          "pluginId": "prometheus",
          "pluginName": "Prometheus"
        }
      ],
      "__requires": [
        {
          "type": "grafana",
          "id": "grafana",
          "name": "Grafana",
          "version": "6.2.4"
        },
        {
          "type": "panel",
          "id": "graph",
          "name": "Graph",
          "version": ""
        },
        {
          "type": "datasource",
          "id": "prometheus",
          "name": "Prometheus",
          "version": "1.0.0"
        },
        {
          "type": "panel",
          "id": "singlestat",
          "name": "Singlestat",
          "version": ""
        }
      ],
      "annotations": {
        "list": [
          {
            "builtIn": 1,
            "datasource": "-- Grafana --",
            "enable": true,
            "hide": true,
            "iconColor": "rgba(0, 211, 255, 1)",
            "name": "Annotations & Alerts",
            "type": "dashboard"
          }
        ]
      },
      "editable": true,
      "gnetId": null,
      "graphTooltip": 0,
      "id": null,
      "links": [],
      "panels": [
        {
          "collapsed": false,
          "gridPos": {
            "h": 1,
            "w": 24,
            "x": 0,
            "y": 0
          },
          "id": 10,
          "panels": [],
          "title": "Invocations",
          "type": "row"
        },
        {
          "cacheTimeout": null,
          "colorBackground": false,
          "colorValue": false,
          "colors": [
            "#299c46",
            "rgba(237, 129, 40, 0.89)",
            "#d44a3a"
          ],
          "datasource": "Prometheus",
          "format": "none",
          "gauge": {
            "maxValue": 100,
            "minValue": 0,
            "show": false,
            "thresholdLabels": false,
            "thresholdMarkers": true
          },
          "gridPos": {
            "h": 3,
            "w": 8,
            "x": 0,
            "y": 1
          },
          "id": 16,
          "interval": null,
          "links": [],
          "mappingType": 1,
          "mappingTypes": [
            {
              "name": "value to text",
              "value": 1
            },
            {
              "name": "range to text",
              "value": 2
            }
          ],
          "maxDataPoints": 100,
          "nullPointMode": "connected",
          "nullText": null,
          "options": {},
          "postfix": "",
          "postfixFontSize": "50%",
          "prefix": "",
          "prefixFontSize": "50%",
          "rangeMaps": [
            {
              "from": "null",
              "text": "N/A",
              "to": "null"
            }
          ],
          "sparkline": {
            "fillColor": "rgba(31, 118, 189, 0.18)",
            "full": false,
            "lineColor": "rgb(31, 120, 193)",
            "show": false
          },
          "tableColumn": "",
          "targets": [
            {
              "expr": "http_server_requests_seconds_count{exception=\"None\",method=\"GET\",outcome=\"SUCCESS\",status=\"200\",uri=\"/api/fruits\",}",
              "format": "time_series",
              "instant": true,
              "intervalFactor": 1,
              "refId": "A"
            }
          ],
          "thresholds": "",
          "title": "Fruits API invocations",
          "type": "singlestat",
          "valueFontSize": "80%",
          "valueMaps": [
            {
              "op": "=",
              "text": "N/A",
              "value": "null"
            }
          ],
          "valueName": "avg"
        },
        {
          "collapsed": false,
          "gridPos": {
            "h": 1,
            "w": 24,
            "x": 0,
            "y": 4
          },
          "id": 8,
          "panels": [],
          "title": "Response Times",
          "type": "row"
        },
        {
          "aliasColors": {},
          "bars": false,
          "dashLength": 10,
          "dashes": false,
          "datasource": "Prometheus",
          "fill": 1,
          "gridPos": {
            "h": 11,
            "w": 8,
            "x": 0,
            "y": 5
          },
          "id": 4,
          "legend": {
            "avg": false,
            "current": false,
            "max": false,
            "min": false,
            "show": true,
            "total": false,
            "values": false
          },
          "lines": true,
          "linewidth": 1,
          "links": [],
          "nullPointMode": "null",
          "options": {},
          "percentage": false,
          "pointradius": 5,
          "points": false,
          "renderer": "flot",
          "seriesOverrides": [],
          "spaceLength": 10,
          "stack": false,
          "steppedLine": false,
          "targets": [
            {
              "expr": "http_server_requests_seconds_sum{exception=\"None\",method=\"GET\",outcome=\"SUCCESS\",status=\"200\",uri=\"/api/fruits\",} / http_server_requests_seconds_count{exception=\"None\",method=\"GET\",outcome=\"SUCCESS\",status=\"200\",uri=\"/api/fruits\",}",
              "format": "time_series",
              "intervalFactor": 1,
              "legendFormat": "Mean response time",
              "refId": "A"
            }
          ],
          "thresholds": [],
          "timeFrom": null,
          "timeRegions": [],
          "timeShift": null,
          "title": "Fruits API - Response Times",
          "tooltip": {
            "shared": true,
            "sort": 0,
            "value_type": "individual"
          },
          "type": "graph",
          "xaxis": {
            "buckets": null,
            "mode": "time",
            "name": null,
            "show": true,
            "values": []
          },
          "yaxes": [
            {
              "format": "short",
              "label": null,
              "logBase": 1,
              "max": null,
              "min": null,
              "show": true
            },
            {
              "format": "short",
              "label": null,
              "logBase": 1,
              "max": null,
              "min": null,
              "show": true
            }
          ],
          "yaxis": {
            "align": false,
            "alignLevel": null
          }
        },
        {
          "collapsed": false,
          "gridPos": {
            "h": 1,
            "w": 24,
            "x": 0,
            "y": 16
          },
          "id": 20,
          "panels": [],
          "title": "JVM insights",
          "type": "row"
        },
        {
          "aliasColors": {},
          "bars": false,
          "dashLength": 10,
          "dashes": false,
          "datasource": "Prometheus",
          "fill": 1,
          "gridPos": {
            "h": 4,
            "w": 8,
            "x": 0,
            "y": 17
          },
          "id": 22,
          "legend": {
            "avg": false,
            "current": false,
            "max": false,
            "min": false,
            "show": true,
            "total": false,
            "values": false
          },
          "lines": true,
          "linewidth": 1,
          "links": [],
          "nullPointMode": "null",
          "options": {},
          "percentage": false,
          "pointradius": 5,
          "points": false,
          "renderer": "flot",
          "seriesOverrides": [],
          "spaceLength": 10,
          "stack": false,
          "steppedLine": false,
          "targets": [
            {
              "expr": "jvm_memory_used_bytes{endpoint=\"8080-tcp\", area=\"heap\"}",
              "format": "time_series",
              "intervalFactor": 1,
              "legendFormat": "{{id}}",
              "refId": "A"
            }
          ],
          "thresholds": [],
          "timeFrom": null,
          "timeRegions": [],
          "timeShift": null,
          "title": "Backend JVM",
          "tooltip": {
            "shared": true,
            "sort": 0,
            "value_type": "individual"
          },
          "type": "graph",
          "xaxis": {
            "buckets": null,
            "mode": "time",
            "name": null,
            "show": true,
            "values": []
          },
          "yaxes": [
            {
              "format": "short",
              "label": null,
              "logBase": 1,
              "max": null,
              "min": null,
              "show": true
            },
            {
              "format": "short",
              "label": null,
              "logBase": 1,
              "max": null,
              "min": null,
              "show": true
            }
          ],
          "yaxis": {
            "align": false,
            "alignLevel": null
          }
        }
      ],
      "refresh": "5s",
      "schemaVersion": 18,
      "style": "dark",
      "tags": [],
      "templating": {
        "list": []
      },
      "time": {
        "from": "now-5m",
        "to": "now"
      },
      "timepicker": {
        "refresh_intervals": [
          "5s",
          "10s",
          "30s",
          "1m",
          "5m",
          "15m",
          "30m",
          "1h",
          "2h",
          "1d"
        ],
        "time_options": [
          "5m",
          "15m",
          "1h",
          "6h",
          "12h",
          "24h",
          "2d",
          "7d",
          "30d"
        ]
      },
      "timezone": "",
      "title": "Fruits Catalog Monitoring",
      "uid": "W_6VDplmz",
      "version": 7
    }
```

## Useful CLI commands


### Manipulating Pods

List pods (without and with nodes):

```sh
oc get pods
oc get pods -o wide
```

Follow pod logs:

```sh
oc logs -f $POD_NAME
```

Exec command into pod:

```sh
oc exec $POD_NAME $COMMAND
```

Interacting directly with the container is simple with `oc rsh`:

```sh
$ oc rsh ruby-ex-5-kgzvd
sh-4.2$ ls
Gemfile Gemfile.lock README.md bundle config.ru
```