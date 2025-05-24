pipeline {
    agent any

    stages {
        stage('Build Artifact') {
            steps {
                sh "mvn clean package -DskipTests=true"
                archiveArtifacts artifacts: 'target/*.jar'
            }
        }

        stage('Unit Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('Docker Build and Push') {
            steps {
                withDockerRegistry([credentialsId: 'docker', url: '']) {
                    sh 'printenv'
                    sh "docker build -t pujitha4698/numeric-app:${GIT_COMMIT} ."
                    sh "docker push pujitha4698/numeric-app:${GIT_COMMIT}"
                }
            }
        }
        stage('Kubernetes Deployment - DEV') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh "sed -i 's#replace#pujitha4698/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                    sh "kubectl apply -f k8s_deployment_service.yaml"
                }
            }
        }

    }
}
