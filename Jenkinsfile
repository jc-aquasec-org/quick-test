timestamps {
    node(){
        checkout scm
        stage("Preparation"){
            sh '''
                find .
                printenv | sort
            '''
        }
        stage('Code Repository Scanned by Aqua') {
            withCredentials([
                string(credentialsId: 'AQUA_KEY', variable: 'AQUA_KEY'),
                string(credentialsId: 'AQUA_SECRET', variable: 'AQUA_SECRET'),
        string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN')
            ]) {
                sh '''
                    export TRIVY_RUN_AS_PLUGIN=aqua
                    export trivyVersion=0.42.0
                    curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b . v${trivyVersion}
                    ./trivy plugin update aqua
                    ./trivy fs --scanners config,vuln,secret . --sast
                '''
            }
        }
        stage('Build Docker Image') {
            // fake build by downloading an image
        // docker pull aquasaemea/mynodejs-app:1.0
            sh '''
            echo the image has been built !!
            '''
        }
        


                '''
                groovy
                pipeline {
                  agent {
                    label 'ubuntu-latest'
                  }
                  environment {
                    DOCKER_REPOSITORY = 'aquajcampbell' // name of Docker Hub ID
                    IMAGE_NAME = 'podman-jc:podman-jc-ubuntu-16.04'
                    IMAGE_TAG = "${env.BUILD_NUMBER}" // $GITHUB_RUN_NUMBER
                  }
                  stages {
                    stage('Execute Tracee') {
                      steps {
                        script {
                          sh 'docker run -e ACCESS_TOKEN=${{ secrets.GITHUB_TOKEN }} -e AQUA_KEY=${{ secrets.AQUA_KEY }} -e AQUA_SECRET=${{ secrets.AQUA_SECRET }} argonsecurity/tracee-commercial-action:main'
                        }
                      }
                    }
                    stage('Checkout code') {
                      steps {
                        script {
                          git 'https://github.com/${{ github.repository }}'
                        }
                      }
                    }
                    stage('Login to DockerHub') {
                      steps {
                        script {
                          sh 'docker login -u ${{ secrets.DOCKERHUB_USERNAME }} -p ${{ secrets.DOCKERHUB_TOKEN }}'
                        }
                      }
                    }
                    stage('Aqua Code Scanning (SCA, IaC, and SAST)') {
                      steps {
                        script {
                          sh 'podman run -e AQUA_KEY=${{ secrets.AQUA_KEY }} -e AQUA_SECRET=${{ secrets.AQUA_SECRET }} -e GITHUB_TOKEN=${{ github.token }} -e TRIVY_RUN_AS_PLUGIN=aqua aquasec/aqua-scanner trivy fs --scanners config,vuln,secret . --sast'
                        }
                      }
                    }
                  }
                }
                '''

Please note that the Jenkinsfile syntax is written in Groovy, so the YAML content has been transformed into Groovy code blocks. Also, make sure to adjust the `argonsecurity/tracee-commercial-action` and `aquasec/aqua-scanner` image versions according to your requirements.
        
     //   stage('Image Scanning by Aqua') {
     //       withCredentials([
     //           string(credentialsId: 'AQUA_REGISTRY_USER', variable: 'AQUA_REGISTRY_USER'),
     //           string(credentialsId: 'AQUA_REGISTRY_PASSWORD', variable: 'AQUA_REGISTRY_PASSWORD'),
     // string(credentialsId: 'AQUA_REGISTRY', variable: 'AQUA_REGISTRY'),
     // string(credentialsId: 'AQUA_HOST', variable: 'AQUA_HOST'),
     // string(credentialsId: 'AQUA_SCANNER_TOKEN', variable: 'AQUA_SCANNER_TOKEN')
     //       ]) {
     //         sh '''
     //         podman run --rm -e $AQUA_KEY -e $AQUA_SECRET -e TRIVY_RUN_AS_PLUGIN=aqua -e SAST=true -e INPUT_WORKING_DIRECTORY=/scanning -e TRIVY_SCANNERS=config,vuln,secret -v /aquajcampbell:/scanning docker.io/aquasec/aqua-scanner trivy fs .
     //         '''
     //      }
     //   }
        stage('Manifest Generation') {
            withCredentials([
                // Replace GITLAB_CREDENTIALS_ID with the id of your gitlab credentials
                string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN'),
                string(credentialsId: 'AQUA_KEY', variable: 'AQUA_KEY'),
                string(credentialsId: 'AQUA_SECRET', variable: 'AQUA_SECRET')
            ]) {
                // Replace ARTIFACT_PATH with the path to the root folder of your project
                // or with the name:tag the newly built image
                    // --artifact-path "aquasaemea/mynodejs-app:1.0"
                sh '''
                  export BILLY_SERVER=https://prod-aqua-billy.codesec.aquasec.com
                    curl -sLo install.sh download.codesec.aquasec.com/billy/install.sh
                    curl -sLo install.sh.checksum https://github.com/argonsecurity/releases/releases/latest/download/install.sh.checksum
                  if ! cat install.sh.checksum | sha256sum ; then
                      echo "install.sh checksum failed"
                      exit 1
                  fi
                  BINDIR="." sh install.sh
                  rm install.sh install.sh.checksum
                    ./billy generate -v \
                        --aqua-key ${AQUA_KEY} \
                        --aqua-secret ${AQUA_SECRET} \
                        --access-token ${GITHUB_TOKEN} \
            --output sbom.json \
                        --artifact-path ./requirements.txt
                '''
            }
        }
        //post {
            //always {
                //archiveArtifacts artifacts: '/'
           // }
        //}
    }
}
