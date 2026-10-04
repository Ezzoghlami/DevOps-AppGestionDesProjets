pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'mohamedzoghlami'
        IMAGE_TAG      = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Build Images') {
            steps { sh 'docker compose build' }
        }

        stage('Push Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                 usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        for svc in backend frontend; do
                          docker tag  ${DOCKERHUB_USER}/projets-$svc:latest ${DOCKERHUB_USER}/projets-$svc:${IMAGE_TAG}
                          docker push ${DOCKERHUB_USER}/projets-$svc:latest
                          docker push ${DOCKERHUB_USER}/projets-$svc:${IMAGE_TAG}
                        done
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d'
            }
        }
    }

    post {
        always { sh 'docker logout || true' }
    }
}
