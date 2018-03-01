Demo Provisioning script
======================

![CI/CD Demo](../../docs/images/cicd-projects.png?raw=true)
![CI/CD Demo](../../docs/images/cicd-pipeline.png?raw=true)

Prerequisites
============
* An OpenShift cluster with sufficient quota and resources

| Demo Name        | Description                                  | Min Memory | Min CPU | Projects |
|------------------|----------------------------------------------|------------|---------|----------|
| msa-min          | Microservices Minimal                        | 4 GB       | 2 cores | 1        |
| msa              | Microservices                                | 8 GB       | 4 cores | 1        |
| msa-cicd-eap-min | Microservices with CI/CD Minimal (Dev-Prod)  | 12 GB      | 6 cores | 3        |
| msa-cicd-eap     | Microservices with CI/CD (Dev-Test-Prod)     | 18 GB      | 8 cores | 5        |

* `oc` client authenticated against an OpenShift cluster

Usage
============
```
Usage:
 provision-demo.sh [command] [demo-name] [options]
 provision-demo.sh --help

Example:
 provision-demo.sh deploy --maven-mirror-url http://nexus.repo.com/content/groups/public/ --project-suffix mydemo

COMMANDS:
   deploy                   Set up the demo projects and deploy demo apps
   delete                   Clean up and remove demo projects and objects
   verify                   Verify the demo is deployed correctly
   idle                     Make all demo servies idle

DEMOS:
   msa                      Microservices app with all services"
   msa-min                  Microservices app with minimum services
   msa-cicd-eap             CI/CD and microservices with JBoss EAP (dev-test-prod)
   msa-cicd-eap-min         CI/CD and microservices with JBoss EAP with minimum services (dev-prod)

OPTIONS:
   --user [username]         The admin user for the demo projects. mandatory if logged in as system:admin
   --maven-mirror-url [url]  Use the given Maven repository for builds. If not specifid, a Nexus container is deployed in the demo
   --project-suffix [suffix] Suffix to be added to demo project names e.g. ci-SUFFIX. If empty, user will be used as suffix
   --ephemeral               Deploy demo without persistent storage
   --run-verify              Run verify after provisioning
```

**NOTE:** OpenShift 3.7 by default uses an older version of Jenkins. Import all Jenkins image tags in order to use the newer Jenkins image
for this demo:
```
$ oc login -u system:admin
$ oc import-image jenkins --from="registry.access.redhat.com/openshift3/jenkins-2-rhel7" --confirm --all -n openshift
$ oc login -u USER
```

Example
============
Provision a minimal demo on a local cluster without persistent storage:
```
$ oc cluster up
$ oc create -f https://raw.githubusercontent.com/jboss-openshift/application-templates/master/jboss-image-streams.json -n openshift
$ provision-demo.sh deploy msa-min --user developer --ephemeral
```

Provision on OpenShift Online/Dedicated and verify afterwards:
```
$ oc login https://api.preview.openshift.com --token=YOUR-TOKEN
$ provision-demo.sh deploy msa-cicd-eap --run-verify
```

Use an existing Sonatype Nexus:
```
$ provision-demo.sh deploy msa --maven-mirror-url http://nexus.repo.com/content/groups/public/
```

Provision demo as ```system:admin``` for user ```john@mycompany.com```:
```
$ oc login -u system:admin
$ provision-demo.sh deploy msa-cicd-eap --user john@mycompany.com
```

Delete demo:
```
$ provision-demo.sh delete msa-cicd-eap
```

Delete demo for user ```john@mycompany.com``` while logged in as ```system:admin```:
```
$ oc login -u system:admin
$ provision-demo.sh delete msa-cicd-eap --user john@mycompany.com
```

Verify demo is deployed correctly:
```
$ provision-demo.sh verify msa-cicd-eap
```

Make all demo services idle
```
$ provision-demo.sh idle msa-cicd-eap
```
