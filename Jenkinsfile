pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@13.200.243.119"
        DEPLOY_PATH = "/home/ubuntu/django-notes-app"
    }

    stages {
        stage('Code') {
            when {
                branch 'main'
            }
            steps {
                echo 'Updating code from main branch...'
                dir('django-notes-app') {
                    script {
                        if (fileExists('.git')) {
                            sh 'git pull origin main'
                        } else {
                            sh 'git clone -b main https://github.com/pwnhcl/django-notes-app.git .'
                        }
                    }
                }
            }
        }

        stage('Build Docker Image') {
            when { branch 'main' }
            steps {
                echo 'Building Docker image...'
                dir('django-notes-app') {
                    sh 'docker build -t django-notes-app:v1 .'
                }
            }
        }

        stage('Copy to Remote Server') {
            when { branch 'main' }
            steps {
                echo 'Copying files to remote server...'
                dir('django-notes-app') {
                    sshagent(['deploy-key']) {
                        sh '''
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} "mkdir -p ${DEPLOY_PATH}"
                        scp -o StrictHostKeyChecking=no -r .env ${DEPLOY_SERVER}:${DEPLOY_PATH}/
                        '''
                    }
                }
            }
        }

        stage('Deploy on Remote Server') {
            when { branch 'main' }
            steps {
                echo 'Deploying remotely...'
                sshagent(['deploy-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} '
                        cd ${DEPLOY_PATH} &&
                        docker-compose down || true &&
                        docker-compose up -d --build
                    '
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
