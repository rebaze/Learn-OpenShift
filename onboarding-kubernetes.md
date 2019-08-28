# Kubernetes Onboarding

## Environment Options

* On Premise (only an option when hardware is available)
* Amazon EKS (more expensive, minimal kubernetes console)
* Google Cloud GKE (cheaper, far better kubernetes default console)

## Considerations

* Adopt JenkinsX -> Opinionated workflows

## Installing Jenkins using Helm

* Make sure we have enough nodes (e.g. using an autoscaling node-pool)
* Install Helm. Recommdation: use local tiller because default installation is not secure: 
    ```
    tiller -listen=localhost:44134 -storage=secret -logtostderr
    export HELM_HOST=localhost:44134
    helm init --client-only
    ```

* run helm install --name my-release stable/jenkins

    ```
    printf $(kubectl get secret --namespace default my-release-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
    1. Get your 'admin' user password by running:
    2. Get the Jenkins URL to visit by running these commands in the same shell:
    NOTE: It may take a few minutes for the LoadBalancer IP to be available.
            You can watch the status of by running 'kubectl get svc --namespace default -w my-release-jenkins'
    export SERVICE_IP=$(kubectl get svc --namespace default my-release-jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")

    echo http://$SERVICE_IP:8080/login

    3. Login with the password from step 1 and the username: admin

    For more information on running Jenkins on Kubernetes, visit:
    https://cloud.google.com/solutions/jenkins-on-container-engine
    ```
* TBD