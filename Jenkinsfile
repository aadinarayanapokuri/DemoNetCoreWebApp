pipeline {  
 agent any  
 environment {  
  DATE = new Date().format('yy.M')
  AWS_ACCOUNT_ID="670166063118"
  AWS_DEFAULT_REGION="ap-northeast-1"
  IMAGE_REPO_NAME="dotnetdemo"
  IMAGE_TAG="${DATE}.${BUILD_NUMBER}"
  REPOSITORY_URI = "670166063118.dkr.ecr.ap-northeast-1.amazonaws.com/dotnetdemo"
  AWS_ECR_REGION = 'ap-northeast-1'
  AWS_ECS_SERVICE = 'ajay111'
  AWS_ECS_CLUSTER = 'dotnet-application'
   }  
 stages {  
 stage('Logging into AWS ECR') {
            steps {
                script {
                sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
                }
                 
            }
        }
  stage('Checkout') {  
   steps {
       git credentialsId: 'github-jenkins', url: 'git@github.com:aadinarayanapokuri/DemoNetCoreWebApp.git', branch: 'main'
   }  
  } 
stage('Docker') {
    steps {    
     script {
          DOCKER_BUILDKIT=1
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }   
            }
        }
  stage('Pushing to ECR') {
     steps{  
         script {
                //sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                //sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
                sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:latest"""
                sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:latest"""
         }
        }
      }
   stage('Deploy in ECS') {
  steps {
      
   sh "aws ecs update-service --cluster ${AWS_ECS_CLUSTER} --service ${AWS_ECS_SERVICE} --force-new-deployment"
      
      }
    }
}
}
