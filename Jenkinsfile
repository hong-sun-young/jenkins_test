node {
    def app


    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        script{
            app = docker.build("hooong2/project")
        }
    }


    stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'DOCKERHUB') {
            app.push("${env.BUILD_NUMBER}")
        }
    }


    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }


    stage('Slack alarm') {
        success {
            slackSend (channel: SLACK_CHANNEL, color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})", teamDomain: TEAM_DOMAIN, tokenCredentialId: TOKEN_CREDENTIAL_ID )
         }

        failure {
            slackSend (channel: SLACK_CHANNEL, color: '#F01717', message: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})", teamDomain: TEAM_DOMAIN, tokenCredentialId: TOKEN_CREDENTIAL_ID )
         }
    }


}
