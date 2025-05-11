pipeline {
    agent any

    tools {
        maven 'Maven3.9,9'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'prod',
                    credentialsId: '25a1b0e5-e71d-44f0-b966-6ad8e6c41a93',
                    url: 'https://github.com/ChaitanyaYeramalla/maven-web-app-project-kk-funda'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SQ Report') {
            steps {
                sh 'mvn clean sonar:sonar'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage('Deploy to Application Tomcat') {
            steps {
                echo 'Deploying WAR file using curl...'
                sh '''
                    curl -u chaitanya:4267@Chaitu \
            --upload-file /var/lib/jenkins/workspace/Jio-scripted-Pl/target/maven-web-application.war \
            "http://3.82.216.212:8080//manager/text/deploy?path=/maven-web-application&update=true"
                '''
            }
        }
    }

    post {
        success {
            script {
                notifyBuild('SUCCESS')
            }
        }
        failure {
            script {
                notifyBuild('FAILED')
            }
        }
        always {
            echo 'Build Finished.'
        }
    }
}

def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus = buildStatus ?: 'SUCCESS'

    def colorCode = '#FF0000'
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    if (buildStatus == 'STARTED') {
        colorCode = '#FFFF00'
    } else if (buildStatus == 'SUCCESS') {
        colorCode = '#00FF00'
    }

    slackSend(color: colorCode, message: summary)
}
