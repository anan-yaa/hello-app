pipeline {
    agent any
    environment {
        IMAGE = 'hello-app'
        TAG   = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
    }
    stages {
        stage('Branch Info') {
            steps { echo "Building branch: ${env.BRANCH_NAME}" }
        }
        stage('Build') {
            steps { bat 'mvn clean compile' }
        }
        stage('Test') {
            steps { bat 'mvn test' }
            post { always { junit 'target/surefire-reports/*.xml' } }
        }
        stage('Package') {
            steps { bat 'mvn package -DskipTests' }
        }
        stage('Docker Build') {
            steps { bat "docker build -t ${IMAGE}:${TAG} ." }
        }
        stage('Deploy') {
            when { branch 'main' }
            steps { bat "docker run --rm ${IMAGE}:${TAG}" }
        }
    }
    post {
        success { echo "Pipeline for ${env.BRANCH_NAME} succeeded!" }
        failure { echo "Pipeline for ${env.BRANCH_NAME} failed." }
    }
}