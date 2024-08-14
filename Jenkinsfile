pipeline {
    agent any

    stages {
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                script {
                    withCredentials([
                        file(credentialsId: 'UTILITIES_INSTANCE_PEM', variable: 'EC2_SSH_KEY'),
                        usernamePassword(credentialsId: 'GITHUB_CRED', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS'),
                        usernamePassword(credentialsId: 'UTILITIES_INSTANCE_CRED', usernameVariable: 'USERNAME', passwordVariable: 'HOST_IP')]) {
                        sh 'cp $EC2_SSH_KEY ./sshkey'
                        sh 'chmod 400 sshkey'
                        sh """
                            ssh -i "sshkey" -o StrictHostKeyChecking=no $USERNAME@$HOST_IP '
                                cd /home/projects/infra-monitor/
                                sudo git checkout main -f
                                sudo git pull https://$GIT_USER:$GIT_PASS@github.com/Amali-Tech/infra-monitor.git
                                docker-compose up  --remove-orphans -d
                            '
                        """
                        }
                }
            }
        }

        stage('CleanUp') {
            steps {
                cleanWs()
            }
        }
    }
}
