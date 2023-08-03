pipeline {
    agent any

    stages {
        stage("Code") {
            steps {
                echo "Cloning the code"
                git url: "https://github.com/prajwal1691/django-notes-app.git", branch: "main"
            }
        }
        stage("build") {
            steps {
                echo "Building the image"
                sh "docker build -t my-note-app:latest ."
            }
        }
        stage("Push to Docker Hub") {
            steps {
                echo "Pushing the image to Docker hub"
                withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPass', usernameVariable: 'dockerHubUser')]) {
                    sh "docker tag my-note-app:latest ${env.dockerHubUser}/my-note-app:v${env.BUILD_NUMBER}"
                    sh "docker tag my-note-app:latest ${env.dockerHubUser}/my-note-app:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/my-note-app:v${env.BUILD_NUMBER}"
                    sh "docker push ${env.dockerHubUser}/my-note-app:latest"
                    sh "docker rmi -f my-note-app:latest"
                    sh "docker rmi -f ${env.dockerHubUser}/my-note-app:v${env.BUILD_NUMBER}"
                    sh "docker rmi -f ${env.dockerHubUser}/my-note-app:latest"
                }
                
            }
        }
        stage("Deploy") {
            steps {
                echo "Deploying to container"
                sh "apt update && apt install -y docker-compose"
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}
