pipeline {
    agent any

    tools {
        maven 'Maven3'   // configure Maven in Jenkins > Global Tool Configuration
        jdk 'JDK11'      // configure JDK as well
    }

    environment {
        SONAR_TOKEN = credentials('Sonar-cloud')  // SonarCloud token stored in Jenkins
        COMMIT_EMAIL = sh(returnStdout: true, script: "git log -1 --pretty=format:'%ae'").trim()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RichardBenjamin/Jenkins_Upgradev3.git'
                echo "Commit email is: ${COMMIT_EMAIL}"
            }
        }


        stage('Build') {
            steps {
                dir('maven-samples/single-module') {
                    sh 'mvn clean install'
                }
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

        stage('SonarCloud Analysis') {
            steps {
                dir('maven-samples/single-module') {
                    withSonarQubeEnv('SonarCloud') {
                        sh """
                            mvn sonar:sonar \
                              -Dsonar.projectKey=RichardBenjamin_Jenkins_Upgradev3 \
                              -Dsonar.organization=richardbenjamin \
                              -Dsonar.host.url=https://sonarcloud.io \
                              -Dsonar.login=$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Dependency Check (OWASP)') {
            steps {
                dir('maven-samples/single-module') {
                    sh """
                        mvn org.owasp:dependency-check-maven:check \
                          -Dformat=ALL \
                          -DoutputDirectory=target/dependency-check-report
                    """
                }
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'maven-samples/single-module/target/*.jar', fingerprint: true
                archiveArtifacts artifacts: 'maven-samples/single-module/target/dependency-check-report/*.*', fingerprint: true
            }
        }
    }

    // post {
    //     failure {
    //         script {
    //         //    def commitEmail = sh(script: 'git log -1 --pretty=%ae', returnStdout: true).trim()

    //             emailext(
    //                 to: "${COMMIT_EMAIL}",
    //                 subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
    //                 body: """Build failed for commit: ${env.GIT_COMMIT}.
    //                          <br>Check logs: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a>""",
    //                 mimeType: 'text/html'
    //             )
    //         }
            
    //     }
    //     success {
    //         script {
    //           //  def commitEmail = sh(script: 'git log -1 --pretty=%ae', returnStdout: true).trim()

    //             emailext(
    //                 to: "${COMMIT_EMAIL}",
    //                 subject: "Build Passed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
    //                 body: "Build passed successfully. <br>Details: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a>",
    //                 mimeType: 'text/html'
    //             )
    //         }
    //     }
    // }
}
