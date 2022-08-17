node {
    def app

    stage('Start') {
            steps {
           	// channel, teamDomain, tokenCredentialId은 젠킨스-슬랙 연동시 쉘 스크립트 문법에서 지원해주는 설정 값들입니다. 이 설정값들에 environment { ... } 에서 작성한 값들을 초기화 해줍니다
                slackSend (channel: SLACK_CHANNEL, color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})", teamDomain: TEAM_DOMAIN, tokenCredentialId: TOKEN_CREDENTIAL_ID )
            }
        }

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

  post {
    success {
         slackSend (channel: SLACK_CHANNEL, color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})", teamDomain: TEAM_DOMAIN, tokenCredentialId: TOKEN_CREDENTIAL_ID )
         }

    failure {
         slackSend (channel: SLACK_CHANNEL, color: '#F01717', message: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})", teamDomain: TEAM_DOMAIN, tokenCredentialId: TOKEN_CREDENTIAL_ID )
         }
    }

}
