pipeline {
  agent any
  environment {
    IMAGE = "sondossoua/monapp"
    PATH = "C:\\Users\\sondo\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin;${env.PATH}"
  }
  stages {
    stage('Build') {
      steps { bat "docker build -t %IMAGE%:latest ." }
    }
    stage('Test') {
      steps {
        bat "docker run -d --name smoke -p 8091:80 %IMAGE%:latest"
        bat "ping -n 6 127.0.0.1 > nul"
        bat "curl -f http://localhost:8091"
      }
      post { always { bat "docker rm -f smoke" } }
    }
    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'U', passwordVariable: 'P')]) {
          bat "docker login -u %U% -p %P%"
          bat "docker push %IMAGE%:latest"
        }
      }
    }
    stage('Deploy') {
      steps { bat "docker-compose up -d" }
    }
  }
}
