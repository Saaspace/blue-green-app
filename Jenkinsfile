pipeline {
  agent any
  environment {
    IMAGE = "saaspace/bluegreen:${BUILD_NUMBER}"  // Docker Hub Username/Repo + build number
    COLOR = "green"                               // Default deploy color
  }
  stages {
    stage('Clone Repo') {
      steps {
        git 'https://github.com/Saaspace/blue-green-app.git'  // Correct GitHub repo
      }
    }
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE .'  // Build docker image
      }
    }
    stage('Push Image') {
      steps {
        withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKER_PASS')]) {
          sh 'echo $DOCKER_PASS | docker login -u saaspace --password-stdin'
          sh 'docker push $IMAGE'
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl set image deployment/myapp-$COLOR myapp=$IMAGE --record'
      }
    }
    stage('Switch Service') {
      steps {
        input "Switch traffic to $COLOR version?"
        sh 'kubectl patch service myapp-service -p "{\"spec\": {\"selector\": {\"app\": \"myapp\", \"color\": \"$COLOR\"}}}"'
      }
    }
  }
}
