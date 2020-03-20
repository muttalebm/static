pipeline{
    agent any
    stages{
        stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
        stage('Upload to AWS') {
              steps {
                  retry(3){         
                    withAWS(region:'ap-southeast-1',credentials:'aws-static') {
                    sh 'echo "Uploading content with AWS creds"'
                        s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'jenkins-awesome-project')
                    }
                  }
              }
         }
         stage('Check if site is up') {
              steps {
                  retry(3){
                      sh 'curl -X GET "https://jenkins-awesome-project.s3-ap-southeast-1.amazonaws.com/index.html"'
                  }
              }
         }
    }
    post {
        always {
            echo 'Finishing up with email report'
            
            emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
                subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
            
        }
    }

}