pipeline {
    agent any

    environment {
        BACKEND_PORT = '8085'
        FRONTEND_PORT = '8084'
        DOCKER_HUB_REPO = 'megha2709'
        BACKEND_IMAGE = "${DOCKER_HUB_REPO}/backend"
        FRONTEND_IMAGE = "${DOCKER_HUB_REPO}/frontend"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/megha270992/project-5.git'
            }
        }

        stage('Build Backend and Frontend') {
            steps {
                script {
                    dir('backend') {
                        sh 'mvn clean install -DskipTests=true'
                    }
                    dir('frontend') {
                        sh 'mvn clean install -DskipTests=true'
                    }
                }
            }
        }

        stage('Package Applications') {
            steps {
                script {
                    sh 'ls backend/target'
                    sh 'ls frontend/target'
                    // Optional: run jars if needed for validation
                    // sh 'java -jar backend/target/backend-1.0-RELEASE.jar &'
                    // sh 'java -jar frontend/target/frontend-1.0-RELEASE.jar &'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    dir('backend') {
                        sh "docker build -t ${BACKEND_IMAGE}:latest ."
                    }
                    dir('frontend') {
                        sh "docker build -t ${FRONTEND_IMAGE}:latest ."
                    }
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'Dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    }
                }
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                script {
                 docker.withRegistry('', 'Dockerhub') {
                    sh "docker push ${BACKEND_IMAGE}:latest"
                    sh "docker push ${FRONTEND_IMAGE}:latest"
                 }
                }
            }
        }
    }
