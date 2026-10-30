
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    stages {

        stage('Checkout Source Code') {
            steps {
                echo 'Checking out source code from GitHub'
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                echo 'Building application using Maven'
                bat 'mvn clean package'
            }
        }

        stage('Archive Build Artifact') {
            steps {
                echo 'Archiving generated WAR file'
                archiveArtifacts artifacts: 'target/devops-cicd-demo.war',
                                 fingerprint: true
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo 'Deploying WAR to Apache Tomcat'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'tomcat-manager',
                        usernameVariable: 'TOMCAT_USER',
                        passwordVariable: 'TOMCAT_PASS'
                    )
                ]) {
                    bat '''
                        curl --fail --show-error --silent ^
                        --user "%TOMCAT_USER%:%TOMCAT_PASS%" ^
                        --upload-file "target\\devops-cicd-demo.war" ^
                        "http://localhost:8090/manager/text/deploy?path=/devops-cicd-demo&update=true"
                    '''
                }
            }
        }
    }
}

