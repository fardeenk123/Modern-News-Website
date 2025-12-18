pipeline {
  agent any

  environment {
    AWS_REGION = "us-east-1"
    ECR_REPO = "370445361629.dkr.ecr.us-east-1.amazonaws.com/newrepo"
  }

  stages {

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t newsapp .'
      }
    }

    stage('Login to ECR & Push') {
      steps {
        sh '''
        aws ecr get-login-password --region $AWS_REGION | \
        docker login --username AWS --password-stdin $ECR_REPO

        docker tag newsapp:latest $ECR_REPO:latest
        docker push $ECR_REPO:latest
        '''
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh 'kubectl apply -f Deployment.yml'
      }
    }
  }
}
