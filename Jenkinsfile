pipeline {
    agent any

    environment {
        def PROJECT = 'pipeline-springboot-jdk17'
        def NAMESPACE = 'dev'
        def ENV = 'dev'
    }
    
    stages{
        stage("SOURCE BUILD"){
            steps{
                sh '/var/jenkins_home/tools/hudson.plugins.gradle.GradleInstallation/gradle-8.13/bin/gradle clean build'
            }
        }

        stage("DOCKER BUILD") {
            steps{
                sh 'podman build --build-arg ENV=$ENV -t ${IMAGE_REPO}/$PROJECT .'
                sh 'podman push ${IMAGE_REPO}/$PROJECT'
                sh 'podman rmi ${IMAGE_REPO}/$PROJECT'
            }
        }

        stage("DEPLOY"){
            steps{
                sh 'helm repo update'
                sh 'helm upgrade --install $PROJECT helm-repo/springchart --create-namespace -n $NAMESPACE --set appName=$PROJECT --set namespace=$NAMESPACE'
                sh 'kubectl rollout restart deployment/$PROJECT -n dev'
            }
        }

        stage("CLEAN WORKSPACE"){
            steps{
                cleanWs()
            }
        }
    }
}
