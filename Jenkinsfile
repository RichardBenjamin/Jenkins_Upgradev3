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

                stage('Publish Test Results') {
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
        failure {
            script {
                // Get the committer's email from the most recent commit
                def commitEmail = sh(script: 'git log -1 --pretty=%ae', returnStdout: true).trim()
                
                emailext(
                    from: 'jenkins@yourdomain.com'
                    to: commitEmail,
                    subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: """Build failed for commit ${env.GIT_COMMIT}.
                             <br>Check logs: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a>""",
                    mimeType: 'text/html'
                )
            }
        }
        success {
            script {
                // Get the committer's email from the most recent commit
                def commitEmail = sh(script: 'git log -1 --pretty=%ae', returnStdout: true).trim()
                
                emailext(
                    from: 'jenkins@yourdomain.com'
                    to: commitEmail,
                    subject: "Build Passed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "Build passed successfully. <br>Details: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a>",
                    mimeType: 'text/html'
                )
            }
        }
    }
}
