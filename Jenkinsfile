pipeline {
    agent {label 'agent1'}

    stages {
        stage('Code') {
            steps {
                echo 'Clonning.....'
                // Add build steps here
                git url: 'https://github.com/pwnhcl/django-notes-app.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
                // Add build steps here
                sh 'whoami'
                sh 'docker build -t django-notes-app:v1 .'
            }
        }
        stage('Pushing') {
            steps {
                echo 'Pushing...'
                // Add push steps here
                withCredentials([usernamePassword(credentialsId: 'DockerHubCred', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) 
                {
                sh 'docker login -u "$DOCKERHUB_USERNAME" -p "$DOCKERHUB_PASSWORD"'
                sh 'docker tag django-notes-app:v1 $DOCKERHUB_USERNAME/django-notes-app:v1'
                sh 'docker push $DOCKERHUB_USERNAME/django-notes-app:v1'
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                // Add deploy steps here
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

