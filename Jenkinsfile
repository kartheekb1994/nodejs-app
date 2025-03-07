pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS='6604c614-77e8-4ddc-ac3e-38dabd5a1e59'
        DOCKER_IMAGE_NAME='kartheekb1994/nodejs-app'
        K8S_NAMESPACE='default'
        K8S_DEPLOYMENT_NAME='nodejs-app-deployment'
        GIT_REPO='https://github.com/kartheekb1994/nodejs-app.git'
    }
    
    stages {
        stage('Clone Repository') {
            steps {
                git url: env.GIT_REPO, branch:'main'
            }
        }
    }
    
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCEKR_IMAGE_NAME}:${env.BUILD_ID}")
            }
        }
    }
    
       stage('Tag Docker Image') {
           steps {
            script {
                docker.tag("${DOCKER_IMAGE_NAME}:${env.BUILD_ID}","${DOCKER_IMAGE_NAME}:latest")
            }
    }
}

       stage('Push Docker Image') {
           steps {
        script {
            docker.withRegistry('https://index.docker.io/v1/',
            DOCKERHUB_CREDENTIALS) {docker.push("${DOCKER_IMAGE_NAME}:${env.BUILD_ID}")
            docker.push("${DOCKER_IMAGE_NAME}:latest")
        
        }
    }
}
}

       stage('Deploy to Kubernetes') {
               steps {
        script {
            sh """
            kubectl set image deployment/${K8S_DEPLOYMENT_NAME} ${K8S_DEPLOYMENT_NAME}=${DOCKER_IMAGE_NAME}:latest--namespace=${K8S_NAMESPACE}
            kubectl rollout status deployment/${K8S_DEPLOYMENT_NAME}--namespace=${K8S_NAMESPACE}
            """
        }
        
    }
}

post {
    success{
        echo 'Pipeline Completed Successfully!'
    }

    failure{
        echo 'pipeline failed'
    }
}

}
