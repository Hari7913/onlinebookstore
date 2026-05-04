pipeline {
    agent any

    environment {
        DOCKER_USER     = "lucky7913"            // ✅ your DockerHub username
        IMAGE_NAME      = "java-onlinebookstore" // ✅ project image name
        IMAGE_TAG       = "latest"
        CONTAINER_NAME  = "HOTSTAR-CONTAINER"
        DOCKER_CREDS = "dockerhub-cred"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/Hari7913/onlinebookstore.git'
            }
        }

        stage('Build Maven Project') {
    steps {
        sh '''
            echo "Building WAR file using Maven..."
            mvn clean package
        '''
    }
}

 stage('JENKINS TO NEXUS') {
        steps {
          withMaven(jdk: 'jdk17', maven: 'maven3', traceability: true) {
             sh 'mvn clean package -DskipTests'
}
        }
    }


        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building Docker image..."
                    docker build -t ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Run Container (Local Test)') {
            steps {
                sh '''
                    echo "Running container locally..."
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p 8089:8080 ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASS')]) {
                    sh '''
                        echo "Logging in to DockerHub..."
                        echo "$DOCKER_HUB_PASS" | docker login -u "$DOCKER_HUB_USER" --password-stdin
                        docker push ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }

       stage('Deploy to Kubernetes') {
    steps {
        sh '''
        rm -rf ~/.kube/config
        aws eks --region ap-south-1 update-kubeconfig --name satyacluster
        kubectl get nodes
        kubectl apply -f Deployment.yml
        kubectl apply -f Service.yml
        '''
    }
}
    }
}
