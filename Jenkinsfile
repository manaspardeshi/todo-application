pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'todo-application-image'
        DOCKERHUB_REPO = 'todo-application'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Maven App') {
            steps{
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t ${DOCKER_IMAGE}:latest .
                '''    
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'manaspardeshi2000',
                        usernameVariable: 'DOCKERHUB_USERNAME',
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKERHUB_PASSWORD" | docker login \
                            -u "$DOCKERHUB_USERNAME" --password-stdin

                        docker tag todo-application-image:latest \
                            "$DOCKERHUB_USERNAME/todo-application:latest"

                        docker push \
                            "$DOCKERHUB_USERNAME/todo-application:latest"

                        docker logout
                    '''        
                }
            }
        }

        stage('Deploy using Docker Compose') {
            steps {
                sh '''
                    docker compose up -d
                '''    
            }
        }

        stage('Verify Deployment') {
            steps{
                sh '''
                    docker ps
                    docker compose ps
                '''    
            }
        }
    }

    post {
        always {
            deleteDir()
        }
    }
}
