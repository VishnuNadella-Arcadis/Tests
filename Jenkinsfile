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


pipeline {
    agent any

    stages {
        stage('SCM') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/VishnuNadella-Arcadis/Tests.git'
            }
        }
        stage('Docker Build') {
            steps {
                // some block
                sh "docker build -t vishnunadella352/demo-aws:latest ."
            }
        }
        stage('Publish ECR'){
            steps{
                withEnv(["AWS_ACCESS_KEY_ID"])
            }
        }
    }
}

















pipeline {
 agent any
 environment {
 AWS_ACCOUNT_ID="778694034991"
 AWS_DEFAULT_REGION="ap-south-1" 
 IMAGE_REPO_NAME="assignment"
 IMAGE_TAG="latest"
 REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
 }
 
 stages {
 
 stage('Logging into AWS ECR') {
 steps {
 script {
 sh "aws ecr get-login-password - region ${AWS_DEFAULT_REGION} | docker login - username AWS - password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
 }
 
 }
 }
 
 stage('Cloning Git') {
 steps {
 checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo.git']]]) 
 }
 }
 
 // Building Docker images
 stage('Building image') {
 steps{
 script {
 dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
 }
 }
 }
 
 // Uploading Docker images into AWS ECR
 stage('Pushing to ECR') {
 steps{ 
 script {
 sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
 sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
 }
 }
 }
 }
}