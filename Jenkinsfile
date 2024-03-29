pipeline {
  agent any
  environment {
    AWS_ACCOUNT_ID = "778694034991"
    AWS_DEFAULT_REGION = "ap-south-1"
    IMAGE_REPO_NAME = "assignment"
    IMAGE_TAG = "latest"
    REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
  }

  stages {

    stage('Logging into AWS ECR') {
      steps {
        script {
          sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
        }

      }
    }

    stage('SCM') {
      steps {
        git branch: 'main', changelog: false, poll: false, url: 'https://github.com/VishnuNadella-Arcadis/Tests.git'
      }
    }

    // Building Docker images
    stage('Building image') {
      steps {
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
      steps {
        script {
          sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
          sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }

    // // This is for docker hub

      stage('Docker Build and Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-creds') {
                        // some block
                        sh "docker build -t vishnunadella352/assignment:latest ."
                        sh "docker push vishnunadella352/assignment:latest"
                    }
                }
            }
        }
    
    // stage('Pushing to Docker Hub') {
    //   steps {
    //     script{
    //       withCredentials([string(credentialsId: 'docker-creds', variable: 'DOCKER-CREDS')]) {
    //         sh 'docker login -u vishnunadella352 -p ${DOCKER-CREDS}'
    //         sh "docker tag assignment:${IMAGE_TAG} vishnunadella352/assignment:${IMAGE_TAG}"
    //         sh "docker push vishnunadella352/assignment:${IMAGE_TAG}"
    //         sh 'docker logout'
    //       }
    //     }
    //   }
    // }

    
    stage('Executing Ansible Script') {
      steps {
        ansiblePlaybook credentialsId: 'assignment-mgmt', disableHostKeyChecking: true, installation: 'Ansible Assignment', inventory: 'inventory.ini', playbook: 'ansible-script.yaml', vaultTmpPath: ''
        // ansiblePlaybook become: true, credentialsId: 'ecr:ap-south-1:AWS creds', installation: 'Ansible', inventory: '/home/ec2-user/learn/inventory.imi', playbook: '/home/ec2-user/learn/ansible-script.yaml', vaultCredentialsId: 'amazon_assignment_creds', vaultTmpPath: ''
        // script{
          
        //   sh "ls"
        //   // sh "echo $pwd"
        //   // sh "echo ${sudo-creds} | sudo -S ${sudo-creds}"
        //   dir("/home/ec2-user"){
        //     sh "ls -l"
        //     sh "echo $pwd"
        //     sh "sudo ansible-playbook -i inventory.imi -c ansible.cfg  ansible-script.yaml"
        //   }
        // }
      }
    }
  }
  // post {
  //   success {
  //     mail to: 'vishnu.nadella@arcadis.com',
  //       subject: "Sucessful Build",
  //       body: "The application is now up and running"
  //   }
  //   failure {
  //     mail to: 'vishnu.nadella@arcadis.com',
  //       subject: "Failed Build",
  //       body: "Deployment was a failure, please take immediate action"
  //   }
  // }
}
