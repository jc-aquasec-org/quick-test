pipeline {
  agent any
  stages {
    stage('Aqua scanner') {
      agent {
        docker {
          image 'aquasec/aqua-scanner'
        }
      }
      steps {
        withCredentials([
          string(credentialsId: 'AQUA_KEY', variable: 'AQUA_KEY'),
          string(credentialsId: 'AQUA_SECRET', variable: 'AQUA_SECRET'),
          string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN')
        ]) {
          script {
            env.TRIVY_RUN_AS_PLUGIN = 'aqua'
            sh '''
              trivy fs --scanners config,vuln,secret .
              # To customize which severities to scan for, add the following flag: --severity UNKNOWN,LOW,MEDIUM,HIGH,CRITICAL
              # To enable SAST scanning, add: --sast
              # To enable reachability scanning, add: --reachability
              # To enable npm/dotnet non-lock file scanning, add: --package-json / --dotnet-proj
              # For http/https proxy configuration add env vars: HTTP_PROXY/HTTPS_PROXY, CA-CRET (path to CA certificate)
            '''
          }
        }

        stage('Build Docker Image') {
            // fake build by downloading an image
            // docker pull aquasaemea/mynodejs-app:1.0
            sh '''
                echo 'the image has been built !!'
            '''
        }
        stage('Manifest Generation') {
            // Replace GITHUB_APP_CREDENTIALS_ID with the id of your github app credentials
            withCredentials([
                string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN'), 
                string(credentialsId: 'AQUA_KEY', variable: 'AQUA_KEY'), 
                string(credentialsId: 'AQUA_SECRET', variable: 'AQUA_SECRET')
            ]) {
                // Replace ARTIFACT_PATH with the path to the root folder of your project 
                // or with the name:tag the newly built image
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
}
}
