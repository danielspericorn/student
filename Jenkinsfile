pipeline {
    agent any

    environment {
        EC2_HOST = credentials('ec2-host')
        EC2_USER = credentials('ec2-user')
        EC2_KEY = credentials('ec2-key')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    nodejs('nodejs') {
                        sh 'npm install'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    nodejs('nodejs') {
                        sh 'npm run build --prod'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'ec2-key', keyFileVariable: 'KEY_FILE')]) {
                        sh """
                            scp -o StrictHostKeyChecking=no -i $KEY_FILE -r dist/your-angular-project $EC2_USER@$EC2_HOST:/var/www/html
                        """
                    }
                }
            }
        }
    }
}
