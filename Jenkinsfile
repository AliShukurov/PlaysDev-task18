pipeline {
    agent {
        label 'host2' 
    }

    stages {
        stage('Clone Git repository') {
            steps {
                git 'https://github.com/AliShukurov/PlaysDev-task18.git' // замените на URL вашего репозитория Git
            }
        }

        stage('Build Docker image') {
            steps {
                sh 'docker-compose build'
            }
        }

        stage('Push Docker image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-cred') {
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

        stage('Run Ansible playbook') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'SSH-for-host3', keyFileVariable: 'SSH_KEY')]) {
                        sh 'ansible-playbook /etc/ansible/ansible_host3_playbook.yml' 
                    }
                }
            }
        }
    }
}
