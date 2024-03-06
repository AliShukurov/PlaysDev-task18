pipeline {
    agent any

    stages {
        stage('Build Docker image') {
            steps {
                sh 'docker-compose build'
            }
        }
        stage('Push Docker image') {
            steps {
                script {
                    docker.withRegistry(credentialsId: 'docker-hub-cred', url: 'https://index.docker.io/v1/') {
                        sh '''
                        docker push alishukurov/nginx-app-t15:latest && docker push alishukurov/apache-app-t15:latest
                        '''
                    }
                }
            }
        }

        stage('Delete docker image locally') {
            steps{
                sh 'docker rmi -f alishukurov/nginx-app-t15:latest && docker rmi -f alishukurov/apache-app-t15:latest'
            }
        }

        stage('Delete all images, networks and containers if exists'){
            agent {
                    label 'ubuntu'
            }
            steps {
                script {
        
                    sh 'docker rm -f $(docker ps -aq) || true'

                    sh 'docker network ls --format "{{.Name}}" | grep -vE "^(bridge|host|none)$" | xargs -r docker network rm'

                    sh 'docker images -q | xargs -r docker rmi -f'
                }
            }
        }
      
        stage('Creating network, run docker containers') {
            agent {
                label 'ubuntu'
            }
            steps {
                script{

                    sh 'docker network create my_network'
                    sh 'docker run -d -p 8080:80 --name apache --network=my_network alishukurov/nginx-app-t15:latest'
                    sh 'docker run -d -p 80:80 --name nginx --network=my_network alishukurov/apache-app-t15:latest'
                }
            }
        }
    }
}
