pipeline {  
 agent any  
 environment {  
  dotnet = 'C:\\Program Files\\dotnet\\dotnet.exe' 
  DATE = new Date().format('yy.M')
  TAG = "${DATE}.${BUILD_NUMBER}"
  AWS_ACCOUNT_ID="670166063118"
  AWS_DEFAULT_REGION="ap-northeast-1"
  IMAGE_REPO_NAME="dotnetdemo"
  IMAGE_TAG="${DATE}.${BUILD_NUMBER}"
  REPOSITORY_URI = "670166063118.dkr.ecr.ap-northeast-1.amazonaws.com/dotnetdemo"
  AWS_ECR_REGION = 'ap-northeast-1'
  AWS_ECS_SERVICE = 'web-application'
  AWS_ECS_TASK_DEFINITION = 'net-application'
  AWS_ECS_COMPATIBILITY = 'FARGATE'
  AWS_ECS_NETWORK_MODE = 'awsvpc'
  AWS_ECS_CPU = '256'
  AWS_ECS_MEMORY = '512'
  AWS_ECS_CLUSTER = 'dotnet-application'
  AWS_ECS_TASK_DEFINITION_PATH = 'adi.json'
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
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }   
            }
        }
  stage('Pushing to ECR') {
     steps{  
         script {
                sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
         }
        }
      }
   stage('Deploy in ECS') {
  steps {
      script {
        sh "aws ecs register-task-definition -–cli-input-json file://adi.json"
        sh "aws ecs update-service –service projectname –task-definition projectname –cluster projectname –force-new-deployment –desired-count 1 –health-check-grace-period-seconds 300"
      }
      }
    }
}
}
