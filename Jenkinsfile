pipeline {
    agent any

    environment {
        TOMCAT_SERVER = 'root@172.31.10.131'
        TOMCAT_DIR = '/root/apache-tomcat-9.0.98/webapps/'
        WAR_FILE = '/var/lib/jenkins/workspace/Pip/target/RCB.war'
        APP_DIR = '/var/lib/jenkins/workspace/Pip/target/RCB'   
        CREDENTIALS_ID = 'ssh'  
    }
    tools{
        maven 'maven'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/chandukulashekar/RCB_webapplication.git' 
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Transfer WAR to Tomcat') {
            steps {
                script {
                    sshagent([CREDENTIALS_ID]) {
                        sh """
                            scp -o StrictHostKeyChecking=no ${WAR_FILE} ${TOMCAT_SERVER}:${TOMCAT_DIR}
                            scp -r -o StrictHostKeyChecking=no ${APP_DIR} ${TOMCAT_SERVER}:${TOMCAT_DIR}
                        """
                    }
                }
            }
        }

        stage('Restart Tomcat') {
            steps {
                script {
                    sshagent([CREDENTIALS_ID]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ${TOMCAT_SERVER} <<EOF
                            cd /root/apache-tomcat-9.0.98/bin
                            ./shutdown.sh
                            sleep 5
                            ./startup.sh
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}
