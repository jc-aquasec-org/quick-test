timestamps {
    node {
        checkout scm
        stage("Preparation") {
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
                '''
            }
        }
        
        stage('Install semgrep') {
            sh '''
                pip3 install semgrep==1.1.0
            '''
        }
        
        stage('Run Trivy with semgrep') {
            sh '''
                ./trivy fs --scanners config,vuln,secret . --sast
            '''
        }
    }
}

        stage('Build Docker Image') {
            // fake build by downloading an image
            // docker pull aquasaemea/mynodejs-app:1.0
            sh '''
                echo 'the image has been built !!'
            '''
        }

        



        
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
    steps {
        // Replace GITHUB_APP_CREDENTIALS_ID with the id of your github app credentials
        withCredentials([
            usernamePassword(credentialsId: 'GITHUB_APP_CREDENTIALS_ID', usernameVariable: 'GITHUB_APP', passwordVariable: 'GITHUB_TOKEN'), 
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
                ./billy generate \
                    --access-token ${GITHUB_TOKEN} \
                    --aqua-key ${AQUA_KEY} \
                    --aqua-secret ${AQUA_SECRET} \
                    --artifact-path "my-image-name:my-image-tag"
                    
                    # The docker image name:tag of the newly built image
                    # --artifact-path "my-image-name:my-image-tag"
                    # OR the path to the root folder of your project. I.e my-repo/my-app 
                    # --artifact-path "ARTIFACT_PATH"
            '''
        }
    }
        //post {
            //always {
                //archiveArtifacts artifacts: '/'
           // }
        //}
        }
