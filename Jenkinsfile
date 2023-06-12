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
                echo 'the image has been built !!'
            '''
        }
    }
}
