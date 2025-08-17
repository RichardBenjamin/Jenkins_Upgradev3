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
               dir('maven-samples/single-module') { 
                  sh 'mvn clean install'
            }
        }
        }

        stage('Debug Workspace') {
            steps {
                  sh 'pwd'
                  sh 'ls -la'
            }
         }

        stage('Test') {
            steps {
               dir('maven-samples/single-module') { 
                  sh 'mvn test'
            }
            }
        }

        stage('Archive Test Reports') {
            steps {
                junit 'maven-samples/single-module/target/surefire-reports/*.xml'  
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'maven-samples/single-module/target/*.jar', fingerprint: true
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
