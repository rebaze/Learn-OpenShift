# Onboarding Openshift

## Cheat Sheet for Rascal Project in Openshift OnDemand

1. Login into an openshift instance.

    > oc login YOURINSTANCE --token=YOURTOKEN

1. Make sure you are in the right project or switch to it:

    > oc project rascal

1. Create the database resource

    > oc new-app -e POSTGRESQL_USER=luke \
                -e POSTGRESQL_PASSWORD=secret \
                -e POSTGRESQL_DATABASE=my_data \
                openshift/postgresql-92-centos7 \
                --name=my-database && oc rollout status dc/my-database -w

1. Build and deploy your application 

    > mvn package fabric8:deploy -Popenshift -Dmaven.test.skip && oc rollout status dc/fruits -w

1. Find endpoint & Use application in browser

    > oc get routes --selector app=fruits

1. Delete it all again

    > oc delete all  --selector app=fruits
    > oc delete all  --selector app=my-database
                    
## Jenkins integration

1. Install jenkins instance
    > oc new-app jenkins-persistent
                  
1. Configure Global Tools

    * maven -> jenkins defaults
    
    * jdk8 -> from https://github.com/AdoptOpenJDK/openjdk8-binaries/releases/download/jdk8u222-b10/OpenJDK8U-jdk_x64_linux_hotspot_8u222b10.tar.gz

1. Set up build pipeline

    * Setup new pipeline-build from https://github.com:rebaze/Learn-OpenShift/                  
                           