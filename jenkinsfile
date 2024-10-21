pipeline {
    agent any

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()  // Clean the workspace to avoid caching issues
            }
        }

        stage('Cloning Code') {
            steps {
                git url: "https://github.com/shubhma1122/django-notes-app.git", branch: "main"
            }
        }

        stage('Build') {
            steps {
                sh "docker build --no-cache -t mynote-app:latest ."  // Force Docker to rebuild without cache
            }
        }

        stage('Push Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                    sh 'docker tag mynote-app:latest $DOCKER_USERNAME/mynote-app:latest'
                    sh 'docker push $DOCKER_USERNAME/mynote-app:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker-compose down'  // Stop and remove old containers
                sh 'docker-compose rm -f'  // Force remove all stopped containers
                sh 'docker-compose up -d'  // Start new containers with the updated image
            }
        }
    }
}
