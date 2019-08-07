[<img src="http://www.rebaze.com/assets/Rebaze_icon_colors_tbg.png" align="right" width="100">](http://rebaze.com)

# An opinionated primer for OpenShift 

## Important external references

* https://learn.openshift.com (an interactive learning tutorial)
* https://manage.openshift.com (Openshift Online for instant access to a OpenShift Instance)
* https://github.com/openshift-katacoda/rhoar-getting-started (Initial example code taken from here)

## Concepts

### Projects [project]:
All resources (except Nodes) exist inside of a project. 
Projects have a list of members and their roles, like viewer, editor, or admin, as well as a set of security controls on the running pods, and limits on how many resources the project can use. 
The names of each resource are unique within a project. Developers may request projects be created, but administrators control the resources allocated to projects.

### Containers
A definition of how to run one or more processes inside of a portable Linux environment. Containers are started from an Image and are usually isolated from other containers on the same machine.
    
### Image
A layered Linux filesystem that contains application code, dependencies, and any supporting operating system libraries. An image is identified by a name that can be local to the current cluster or point to a remote Docker registry (a storage server for images).

### Pods
A set of one or more containers that are deployed onto a Node together and share a unique IP and Volumes (persistent storage). Pods also define the security and runtime policy for each container.

### Labels
Labels are key value pairs that can be assigned to any resource in the system for grouping and selection. Many resources use labels to identify sets of other resources.

### Volumes:
Containers are not persistent by default - on restart their contents are cleared. Volumes are mounted filesystems available to Pods and their containers which may be backed by a number of host-local or network attached storage endpoints. The simplest volume type is EmptyDir, which is a temporary directory on a single machine. Administrators may also allow you to request a Persistent Volume that is automatically attached to your pods.

### Services [svc]:
A name representing a set of pods (or external servers) that are accessed by other pods. The service gets an IP and a DNS name, and can be exposed externally to the cluster via a port or a Route. It's also easy to consume services from pods because an environment variable with the name <SERVICE>_HOST is automatically injected into other pods.

### Routes [route]:
A route is an external DNS entry (either a top level domain or a dynamically allocated name) that is created to point to a service so that it can be accessed outside the cluster. The administrator may configure one or more Routers to handle those routes, typically through an Apache or HAProxy load balancer / proxy.

### Deployment Configuration [dc]:
Defines the template for a pod and manages deploying new images or configuration changes whenever those change. A single deployment configuration is usually analogous to a single micro-service. Can support
many different deployment patterns, including full restart, customizable rolling updates, and fully custom behaviors, as well as pre- and post-hooks. Each deployment is represented as a replication controller.

### Build Configuration [bc]:
Contains a description of how to build source code and a base image into a new image - the primary method for delivering changes to your application.
Builds can be source based and use builder images for common languages like Java, PHP, Ruby, or Python, or be Docker based and create builds from a Dockerfile. Each build configuration has web-hooks and can be triggered automatically by changes to their base images.

### Builds [build]:
Builds create a new image from source code, other images, Dockerfiles, or binary input. A build is run inside of a container and has the same restrictions normal pods have. A build usually results in an image pushed to a Docker registry, but you can also choose to run a post-build test that does not push an image.

### Replication Controllers [rc]:
A replication controller maintains a specific number of pods based on a template that match a set of labels. If pods are deleted (because the node they run on is taken out of service) the controller creates a new copy of that pod. A replication controller is most commonly used to represent a single deployment of part of an application based on a built image.

### Image Streams and Image Stream Tags [is,istag]:
An image stream groups sets of related images under tags - analogous to a branch in a source code repository. Each image stream may have one or more tags (the default tag is called "latest") and those tags may point at external Docker registries, at other tags in the same stream, or be controlled to directly point at known images. In addition, images can be pushed to an image stream tag directly via the integrated Docker registry.

### Secrets [secret]:
The secret resource can hold text or binary secrets for delivery into your pods. By default, every container is given a single secret which contains a token for accessing the API (with limited privileges) at /var/run/secrets/kubernetes.io/serviceaccount. You can create new secrets and mount them in your own pods, as well as reference secrets from builds (for connecting to remote servers) or use them to import remote images into an image stream.

### Nodes [node]:
Machines set up in the cluster to run containers. Usually managed by administrators and not by end users.


## Cheat Sheet for Rascal Project

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
                           
                          