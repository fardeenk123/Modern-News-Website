pipeline {
  agent any

  environment {
    AWS_REGION = "us-east-1"
    ECR_REPO = "891377221285.dkr.ecr.us-east-1.amazonaws.com/newrepo"
  }

  stages {
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t newsapp .'
      }
    }

    stage('Login to ECR & Push') {
      steps {
        withCredentials([
          string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
          string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
        ]) {
          sh '''
          export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
          export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
          
          aws ecr get-login-password --region $AWS_REGION | \
          docker login --username AWS --password-stdin $ECR_REPO

          docker tag newsapp:latest $ECR_REPO:latest
          docker push $ECR_REPO:latest
          '''
        }
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh '''
        kubectl apply -f Deployment.yml
        kubectl apply -f Service.yml
        '''
      }
    }
  }
}
