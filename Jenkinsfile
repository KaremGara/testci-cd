pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "testci-cd:${env.BUILD_ID}"
        DOCKER_REGISTRY = "my-docker-registry"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/KaremGara/testci-cd.git' 
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.build("${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}")
                }
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm ${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE} npm test'
            }
        }

        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry('https://karemghara', 'docker-credentials-id') {
                        docker.image("${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "helm upgrade --install testci-cd ./helm-chart --set image.repository=${env.DOCKER_REGISTRY}/my-web-app --set image.tag=${env.BUILD_ID}"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
