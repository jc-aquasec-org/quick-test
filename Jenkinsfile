pipeline {
  agent any
  
  stages {
    stage('Checkout code') {
      steps {
        git branch: 'main', url: 'https://github.com/jc-aquasec-org/quick-test.git', installation: 'my-git'
      }
    }
    
    stage('Login to DockerHub') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
            docker.login(username: credentials('DOCKER_USERNAME'), password: credentials('DOCKER_TOKEN'))
          }
        }
      }
    }
    
    stage('Aqua Code Scanning (SCA, IaC, and SAST)') {
      steps {
        script {
          docker.image('aquasec/aqua-scanner').inside {
            withEnv([
              'AQUA_KEY=${secrets.AQUA_KEY}',
              'AQUA_SECRET=${secrets.AQUA_SECRET}',
              'GITHUB_TOKEN=${github.token}',
              'TRIVY_RUN_AS_PLUGIN=aqua'
            ]) {
              sh 'trivy fs --scanners config,vuln,secret . --sast'
            }
          }
        }
      }
    }
  }
}
