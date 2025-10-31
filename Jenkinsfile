pipeline {
    agent any

    environment {
        def PROJECT = 'workout-kubernetes-deployment-springboot-gradle'
        def ENV = 'dev'
    }

    stages{
        stage("PROJECT BUILD"){
            steps{
                sh '/var/jenkins_home/tools/hudson.plugins.gradle.GradleInstallation/gradle-8.13/bin/gradle clean build'
            }
        }

        stage("IMAGE BUILD") {
            steps{
                sh 'podman build --build-arg ENV=$ENV -t ${IMAGE_REPO}/$ENV/$PROJECT:$(git rev-parse --short HEAD) .'
                sh 'podman push ${IMAGE_REPO}/$ENV/$PROJECT:$(git rev-parse --short HEAD)'
                sh 'podman rmi ${IMAGE_REPO}/$ENV/$PROJECT:$(git rev-parse --short HEAD)'
            }
        }

        stage("DEPLOY"){
            steps{
                sh 'helm repo update'
                sh 'helm upgrade $PROJECT helm-repo/springchart --install --atomic --timeout 1m --wait --create-namespace -n $ENV --set appName=$PROJECT --set namespace=$ENV --set image.tag=$(git rev-parse --short HEAD)'
                sh 'kubectl rollout restart deployment/$PROJECT -n dev'
            }
        }

        stage("CLEAN"){
            steps{
                cleanWs()
            }
        }
    }

}
