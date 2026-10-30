// Pipeline CI : construit les 3 images, les tague et les envoie sur Docker Hub.
pipeline {
    agent any                          // utilise l'agent Jenkins par defaut (qui a docker)

    environment {
        DOCKERHUB_USER = 'mamadoutall' // mon compte Docker Hub
    }

    stages {

        // Stage 1 : recuperer le code depuis le depot Git
        stage('Checkout') {
            steps {
                checkout scm           // utilise le depot configure dans le job
            }
        }

        // Stage 2 : construire les 3 images Docker
        stage('Build') {
            steps {
                sh 'docker build -t bijouterie-frontend ./frontend'
                sh 'docker build -t bijouterie-backend ./backend'
                sh 'docker build -t bijouterie-mysql ./database'
            }
        }

        // Stage 3 : donner a chaque image 2 tags (numero de build + latest)
        stage('Tag') {
            steps {
                sh 'docker tag bijouterie-frontend $DOCKERHUB_USER/bijouterie-frontend:$BUILD_NUMBER'
                sh 'docker tag bijouterie-frontend $DOCKERHUB_USER/bijouterie-frontend:latest'
                sh 'docker tag bijouterie-backend $DOCKERHUB_USER/bijouterie-backend:$BUILD_NUMBER'
                sh 'docker tag bijouterie-backend $DOCKERHUB_USER/bijouterie-backend:latest'
                sh 'docker tag bijouterie-mysql $DOCKERHUB_USER/bijouterie-mysql:$BUILD_NUMBER'
                sh 'docker tag bijouterie-mysql $DOCKERHUB_USER/bijouterie-mysql:latest'
            }
        }

        // Stage 4 : se connecter a Docker Hub et envoyer les images
        stage('Push') {
            steps {
                // le login et le token viennent du credential Jenkins
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh 'echo $DH_PASS | docker login -u $DH_USER --password-stdin'
                    sh 'docker push $DOCKERHUB_USER/bijouterie-frontend:$BUILD_NUMBER'
                    sh 'docker push $DOCKERHUB_USER/bijouterie-frontend:latest'
                    sh 'docker push $DOCKERHUB_USER/bijouterie-backend:$BUILD_NUMBER'
                    sh 'docker push $DOCKERHUB_USER/bijouterie-backend:latest'
                    sh 'docker push $DOCKERHUB_USER/bijouterie-mysql:$BUILD_NUMBER'
                    sh 'docker push $DOCKERHUB_USER/bijouterie-mysql:latest'
                }
            }
        }
    }

    // Apres le pipeline (succes ou echec) : on se deconnecte de Docker Hub
    post {
        always {
            sh 'docker logout'
        }
    }
}