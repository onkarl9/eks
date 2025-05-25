pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = 'docker-hub'  // replace it with your Jenkins Credentials ID
        IMAGE_NAME = 'binsampleapp'
        IMAGE_TAG = 'latest'
    }
    stages {
        stage('Build and Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, usernameVariable: 'DOCKERHUB_USERNAME', 
passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        sh "docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASSWORD}"
                        sh "docker build -t ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG} ."
                        sh "docker tag ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG} index.docker.io/${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                        sh "docker push index.docker.io/${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }
        stage('K8s App Deploy') {
            steps {
                    sh '''
                    kubectl version --client
                    whoami
                    pwd
                    kubectl config view --kubeconfig /var/lib/jenkins/.kube/config
                    kubectl apply -f nginx-deployment.yml --kubeconfig=/var/lib/jenkins/.kube/config
                    kubectl config get-contexts
                    '''
                }
            }
        } 
    post {
        always {
            echo 'Pipeline executed successfully'
        }
    }
}
