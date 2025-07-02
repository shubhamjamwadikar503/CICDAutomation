pipeline {
  agent any

  environment {
    DOCKERHUB_REPO = "shubhamjamwadikar/sales-dashboard"
    DOCKER_TAG = "${BUILD_NUMBER}"
  }

  stages {
    stage('Clone Code') {
      steps {
        git credentialsId: 'github', url: 'https://github.com/shubhamjamwadikar503/CICDAutomation.git', branch: 'developer'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          docker.build("${DOCKERHUB_REPO}:${DOCKER_TAG}")
          docker.build("${DOCKERHUB_REPO}:latest")
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        echo 'Pushing docker image to Docker Hub'
        withCredentials([usernamePassword(credentialsId: 'dockerhub-token', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          script {
            sh '''
              echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
              docker push ${DOCKERHUB_REPO}:${DOCKER_TAG}
              docker push ${DOCKERHUB_REPO}:latest
            '''
          }
        }
      }
    }

    stage('Deploy Docker Image') {
      steps {
        echo 'Deploying image'
        script {
          sh '''
            docker stop sales-dashboard || true
            docker rm sales-dashboard || true
            docker pull ${DOCKERHUB_REPO}:latest
            docker run -d --name sales-dashboard -p 5000:5000 ${DOCKERHUB_REPO}:latest
          '''
        }
      }
    }
  }
}
