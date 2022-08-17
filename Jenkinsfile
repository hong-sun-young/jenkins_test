node {

    def notifySlack(STATUS, COLOR) {
    slackSend channel: SLACK_CHANNEL, 
	  message: STATUS+" : " + "${env.JOB_NAME}[${env.BUILD_NUMBER}] (${env.BUILD_URL})", 
	  color: COLOR, tokenCredentialId: TOKEN_CREDENTIAL_ID, 
	  teamDomain: TEAM_DOMAIN
}

    try {
        // 빌드 시작을 알리는 메시지
        notifySlack("STARTED", "#0000FF")


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

    // 빌드 성공시
    notifySlack("SUCCESS", "#00FF00")
 	  } catch(e) {
    // 빌드 실패시
    notifySlack("FAILED", "#FF0000")
	  }

}
