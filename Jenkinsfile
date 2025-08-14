pipeline {
    agent any

    tools {
        maven 'Maven3'   // Your configured Maven in Jenkins
        jdk 'JDK17'      // Your configured JDK in Jenkins
    }

    environment {
        DOCKER_IMAGE = "pavanreddych/javabank:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Pavanreddy56/hello-java.git'
            }
        }

        stage('Build with Maven') {
            steps {
                bat 'mvn clean package -DskipTests'
                bat 'dir target'  // ✅ Verify jar exists
            }
        }

        stage('Prepare Docker Context') {
            steps {
                // Copy jar from target/ to workspace root for Docker build context
                bat 'copy target\\*.jar .'
                bat 'dir' // ✅ List files in workspace to confirm jar is here
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_IMAGE%'
            }
        }
    }
}
