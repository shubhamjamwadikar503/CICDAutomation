pipeline {
  agent any

  environment {
    DOCKERHUB_REPO = "shubhamjamwadikar/sales-dashboard"
    DOCKER_TAG = "${BUILD_NUMBER}"
    DOCKERHUB_CREDENTIALS = credential('dockerhub-token')
 //   AWS_REGION = 'us-east-1'
    // EKS_CLUSTER_NAME = 'your-eks-cluster-name'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          def image = docker.build("${DOCKERHUB_REPO}:${DOCKER_TAG}")
          docker.build("${DOCKERHUB_REPO}:latest")
        }
      }
    }

    stage('Push Docker Image') {
      steps {
          echo 'pushing docker image to docker hub'
          script {
             sh "docker push ${DOCKERHUB_REPO}:${DOCKER_TAG}"
             sh "docker push ${DOCKERHUB_REPO}:latest"
            }
          }
        }
    stage('Deploy Docker Image') {
      steps {
          echo 'Deploying image'
          script {
             sh "docker stop sales-dashboard || true"
             sh "docker rm sales-dashboard || true"
             sh "docker pull ${DOCKERHUB_REPO}:latest"
             sh "docker run -d --name sale-dashboard -p 5000:5000 ${DOCKERHUB_REPO}:latest"
            }
          }
        }

    }
}

/*
    stage('Deploy to EKS') {
      steps {
        withAWS(region: "${AWS_REGION}", credentials: 'aws-eks-creds') {
          script {
            sh """
              aws eks update-kubeconfig --region ${AWS_REGION} --name ${EKS_CLUSTER_NAME}
              kubectl apply -f k8s/deployment.yaml
            """
          }
        }
      }
    }
*/
