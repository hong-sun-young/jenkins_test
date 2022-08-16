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


}

