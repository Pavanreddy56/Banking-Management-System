pipeline {
    agent any

    tools {
        maven 'Maven3'   // Name from Jenkins Global Tool Configuration
        jdk 'JDK17'      // Name from Jenkins Global Tool Configuration
    }

    environment {
        DOCKER_IMAGE = "pavanreddych/javabank:latest"
        APP_DIR = "Banking-Management-System" // Repo folder containing pom.xml
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Pavanreddy56/Banking-Management-System.git'
            }
        }

        stage('Build with Maven') {
            steps {
                dir("${APP_DIR}") {
                    bat 'mvn clean package -DskipTests'
                    bat 'dir target'  // ✅ Verify JAR exists
                }
            }
        }

        stage('Prepare Docker Context') {
            steps {
                // Copy JAR to workspace root so Docker build context can find it
                bat "copy ${APP_DIR}\\target\\*.jar ."
                bat 'dir' // ✅ Confirm jar is here
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds', 
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS'
                )]) {
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
