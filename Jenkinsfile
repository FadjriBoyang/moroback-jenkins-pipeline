pipeline {
    agent any

    tools {
        go 'go-1.21.4'
    }
        
    environment {
        ANSIBLE_CONFIG = '/etc/ansible/ansible.cfg'
        PRIVATE_KEY_PATH = '/etc/ansible/morokey.pem'
        ANSIBLE_LOCAL_TEMP = '/tmp/ansible_tmp'
    }

    stages {
        stage('Debug') {
            steps {
                script {
                    sh 'whoami'

                    sh 'ls -l $PRIVATE_KEY_PATH'
                    sh 'cat $PRIVATE_KEY_PATH'

                }
            }
        }
        stage('Unit Test') {
            steps {
                script {
                    sh 'go mod init main'
                    
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'pwd'
                    sh 'ls'
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
