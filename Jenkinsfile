pipeline {
    agent any

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'master',
                url: 'https://github.com/SHETTY1941/node-docker-app.git'
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
                docker build -t node-docker-app:${BUILD_NUMBER} .
                docker tag node-docker-app:${BUILD_NUMBER} king1941/node-docker-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push king1941/node-docker-app:${BUILD_NUMBER}'
            }
        }

        stage('Cleanup Old Containers') {
            steps {
                sh '''
                docker ps -aq --filter "name=node-docker-app" | xargs -r docker rm -f
                '''
            }
        }

        stage('Create Container') {
            steps {
                sh 'docker run -d -p 3000:3000 --name node-docker-app king1941/node-docker-app:${BUILD_NUMBER}'
            }
        }

    }
}
