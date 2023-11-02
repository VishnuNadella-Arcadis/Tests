pipeline {
    agent any

    stages {
        stage('SCM') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/VishnuNadella-Arcadis/Tests.git'
            }
        }
        stage('Docker Build and Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'DockerHub') {
                        // some block
                        sh "docker build -t vishnunadella352/demo-aws:latest ."
                        sh "docker push vishnunadella352/demo-aws:latest"
                    }
                }
            }
        }
    }
}
