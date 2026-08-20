pipeline{
    agent {
        label "agent-1"
    }
    environment {
        IMAGE_NAME = "abhishekkargeti/myauthserviceimage"
        IMAGE_TAG  = "${BUILD_NUMBER}"
        NAMESPACE  = "authserver"
        DEPLOYMENT = "auth-server-deployment"
        CONTAINER  = "auth-server-app"
    }
    stages{
        stage("Code Cloning"){
            steps{
                sh 'echo "Code Cloning"'
                git url :"https://github.com/abhishekkargeti1/CI-CD-Jenkins-Docker-Kubernetes-.git",branch:"main"
            }
        }
        stage("Building-JAR"){
            steps{
                sh 'echo "Code Building"'
                sh 'mvn clean package -DskipTests'
            }
        }
        stage("Building-Docker-Image"){
            steps{
                sh 'echo "Docker Image Building"'
                sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }
        stage("Testing"){
            steps{
                sh 'echo "Code Testing"'
            }
        }
        stage("Pushing-Docker-Image"){
            steps{
                sh 'echo "Pushing Docker Image"'
            withCredentials([
             usernamePassword(
                 credentialsId: 'DockerCred',
                 usernameVariable: 'DOCKER_USERNAME',
                 passwordVariable: 'DOCKER_PASSWORD'
            )
                ]){
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                    sh 'docker image tag authserviceimages:latest  abhishekkargeti/authserviceimages:latest'
                    sh 'docker push abhishekkargeti/authserviceimages:latest '
                    sh 'echo "Image Push Successfully"'
                }
            }
        } 
        stage("Deployment"){
            steps{
                sh 'echo "Code Deployment"'
                sh 'kubectl apply -f auth-server-deployment.yml '
                
        }
        stage("Verify Deployment") {
            steps {
                echo "Checking Kubernetes deployment..."

                sh """
                    kubectl get nodes
                    kubectl get pods -n ${NAMESPACE} -o wide
                    kubectl get svc -n ${NAMESPACE}
                    kubectl get deployment -n ${NAMESPACE}
                """
            }
        }
    }
}

 post {

        success {
            echo "CI/CD pipeline completed successfully!"
        }

        failure {
            echo "CI/CD pipeline failed!"
        }
    }



}