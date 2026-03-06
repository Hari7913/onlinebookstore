pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Hari7913/onlinebookstore.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t onlinebookstore:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker rm -f onlinebookstore-container || true

                docker network create bookstore-net || true

                docker volume create bookstore-data || true

                docker run -d \
                --name onlinebookstore-container \
                -p 8088:8080 \
                --network bookstore-net \
                -v bookstore-data:/usr/local/tomcat/webapps \
                onlinebookstore:latest
                '''
            }
        }
    }
}
