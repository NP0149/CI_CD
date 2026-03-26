pipeline {
    agent any

    environment {
        DOCKER_HOST = "unix:///home/user/.docker/desktop/docker.sock"
    }

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/NP0149/CI_CD.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t k8n:${BUILD_NUMBER} .
                docker tag k8n:${BUILD_NUMBER} navyapasunuri06/ci_cd:${BUILD_NUMBER}
                '''
            }
        }

        stage('Docker Login & Push') {
            steps {
                // Inject Docker token from Jenkins Credentials
                withCredentials([string(credentialsId: 'DOCKER_HUB_TOKEN', variable: 'DOCKER_TOKEN')]) {
                    sh '''
                    echo $DOCKER_TOKEN | docker login -u navyapasunuri06 --password-stdin
                    docker push navyapasunuri06/ci_cd:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                # Stop existing container if running
                docker stop k8n-container || true

                # Remove existing container if exists
                docker rm k8n-container || true

                # Run new container
                docker run -d -p 3000:8080 --name k8n-container navyapasunuri06/ci_cd:${BUILD_NUMBER}
                '''
            }
        }
    }
}
