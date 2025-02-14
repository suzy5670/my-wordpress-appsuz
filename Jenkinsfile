pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPOSITORY = 'my-wordpress-appsuz'
        IMAGE_TAG = 'latest'
        ECR_REGISTRY = '<ton-registry>.dkr.ecr.us-east-1.amazonaws.com'
        DOCKER_IMAGE = "$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG"
    }

    stages {
        stage('Cloner le repo') {
            steps {
                git branch: 'main', url: 'https://github.com/suzy5670/my-wordpress-appsuz.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $ECR_REPOSITORY:$IMAGE_TAG .'
            }
        }

        stage('Authentification AWS ECR') {
            steps {
                sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REGISTRY'
            }
        }

        stage('Push to AWS ECR') {
            steps {
                sh 'docker tag $ECR_REPOSITORY:$IMAGE_TAG $DOCKER_IMAGE'
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Déploiement avec Ansible') {
            steps {
                sh 'ansible-playbook -i inventory/aws_ec2.yml deploy.yml'
            }
        }

        stage('Nettoyage') {
            steps {
                sh 'docker rmi $DOCKER_IMAGE'
            }
        }
    }
}
