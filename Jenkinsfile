pipeline {
  agent any
  stages {
    stage('Checkout code') {
      steps {
        checkout scm
      }
    }
    stage('Run Aqua scanner') {
      steps {
        withCredentials([
          string(credentialsId: 'AQUA_KEY', variable: 'AQUA_KEY'),
          string(credentialsId: 'AQUA_SECRET', variable: 'AQUA_SECRET'),
          string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN')
        ]) {
          script {
            env.TRIVY_RUN_AS_PLUGIN = 'aqua'
            env.PACKAGE_JSON = "1"
            env.SAST = "1"
            sh '''
              docker run --rm
                -e AQUA_KEY=${AQUA_KEY}
                -e AQUA_SECRET=${AQUA_SECRET}
                -e GITHUB_TOKEN=${GITHUB_TOKEN}
                -e TRIVY_RUN_AS_PLUGIN=${TRIVY_RUN_AS_PLUGIN}
                -e PACKAGE_JSON=${PACKAGE_JSON}
                -e SAST=${SAST}
                aquasec/aqua-scanner trivy fs --scan-type fs --security-checks vuln,config,secret --hide-progress false --format table --severity MEDIUM,HIGH,CRITICAL .
            '''
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
