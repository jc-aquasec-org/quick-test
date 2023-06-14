pipeline {
  agent any
  
  stages {
    stage('Checkout code') {
      steps {
        git branch: 'main', url: 'https://github.com/jc-aquasec-org/quick-test.git', tool: 'System Git'
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

    stage('Build Docker Image') {
      steps {
        sh '''
          echo 'the image has been built !!'
        '''
      }
    }

    stage('Manifest Generation') {
      steps {
        withCredentials([
          string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN'),
          string(credentialsId: 'AQUA_KEY', variable: 'AQUA_KEY'),
          string(credentialsId: 'AQUA_SECRET', variable: 'AQUA_SECRET')
        ]) {
          sh '''
            export BILLY_SERVER=https://billy.codesec.aquasec.com
            curl -sLo install.sh download.codesec.aquasec.com/billy/install.sh
            curl -sLo install.sh.checksum https://github.com/argonsecurity/releases/releases/latest/download/install.sh.checksum
            if ! cat install.sh.checksum | sha256sum ; then
                echo "install.sh checksum failed"
                exit 1
            fi
            BINDIR="." sh install.sh
            rm install.sh install.sh.checksum
            ./billy health - v \
                --access-token ${GITHUB_TOKEN} \
                --aqua-key ${AQUA_KEY} \
                --aqua-secret ${AQUA_SECRET} \
                --artifact-path ./requirements.txt

                # The docker image name:tag of the newly built image
                # --artifact-path "my-image-name:my-image-tag"
                # OR the path to the root folder of your project. I.e my-repo/my-app 
                # --artifact-path "ARTIFACT_PATH"
          '''
        }
      }
    }
