pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RichardBenjamin/Jenkins_Upgradev3.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
               dir('Jenkins_Upgradev3/maven-samples/single-module') { 
                  sh 'mvn clean install'
            }
            }
        }
    }

    post {
        success {
            echo 'Build and Tests successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
