pipeline {
    agent any

    tools {
        go 'go1.21.5'
    }
        
    environment {
        ANSIBLE_CONFIG = '/etc/ansible/ansible.cfg'
        PRIVATE_KEY_PATH = '/etc/ansible/morokey.pem'
    }

    stages {
        stage('Debug') {
            steps {
                script {
                    sh 'whoami'
                    sh 'ls -l $PRIVATE_KEY_PATH'
                    sh 'cat $PRIVATE_KEY_PATH'
                    sh 'ansible-playbook ansible/deploy-container.yaml'
                }
            }
        }
        stage('Unit Test') {
            steps {
                script {
                    sh 'go mod init hello'
                    
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'go build -o main .'
                }
                archiveArtifacts 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t boyangyang/moroback:latest .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_REGISTRY_CREDENTIALS_ID', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh """
                            echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin
                            docker push boyangyang/moroback:latest
                            
                        """
                    }
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    sh 'ansible-playbook ansible/deploy-container.yaml'
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
