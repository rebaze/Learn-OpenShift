pipeline {
    environment {
    PROJECT = "Golemites"
    APP_NAME = "fruits"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "europe-west1-b"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
  }

    agent {
    kubernetes {
      label 'fruits-app-build'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  # serviceAccountName: cd-jenkins
  containers:
  - name: maven
            image: gcr.io/golemite/fabrik/fabrik-builder-maven:d543915
    command:
    - cat
    tty: true
  #- name: gcloud
  #  image: gcr.io/cloud-builders/gcloud
  #  command:
  #  - cat
  #  tty: true
  #- name: kubectl
  #  image: gcr.io/cloud-builders/kubectl
  #  command:
  #  - cat
  #  tty: true
"""
    }
}
    stages {
        stage ('Initialize') {
            steps {
                container('maven') {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    cat /usr/share/maven/ref/settings.xml
                '''
                }
            }
        }

        stage ('Build') {
            steps {
                container('maven') {
                    sh 'mvn -s /usr/share/maven/ref/settings.xml -Dmaven.test.failure.ignore=true install' 
                }
            }
            post {
                success {
                    junit 'target/surefire-reports/**/*.xml' 
                }
            }
        }
    }
}