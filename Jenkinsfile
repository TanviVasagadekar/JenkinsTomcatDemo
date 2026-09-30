pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'JDK21'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Archive WAR') {
            steps {
                archiveArtifacts artifacts: 'target/*.war',
                                 fingerprint: true
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'tomcat-credentials',
                        usernameVariable: 'TOMCAT_USER',
                        passwordVariable: 'TOMCAT_PASSWORD'
                    )
                ]) {
                    sh '''
                        curl --fail \
                        -u "$TOMCAT_USER:$TOMCAT_PASSWORD" \
                        --upload-file target/JenkinsTomcatDemo.war \
                        "http://localhost:8081/manager/text/deploy?path=/JenkinsTomcatDemo&update=true"
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'curl --fail http://localhost:8081/JenkinsTomcatDemo/'
            }
        }
    }
}
