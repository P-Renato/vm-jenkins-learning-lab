pipeline {
    agent any

    environment {
        APP_SERVER = "192.168.122.93"
        APP_USER = "app-server"
        SSH_KEY = "/var/lib/jenkins/ssh_key"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/P-Renato/vm-jenkins-learning-lab.git'
            }
        }

        stage('Verify Files') {
            steps {
                sh 'ls -la static-site'
            }
        }

        stage('Deploy to App Server') {
            steps {
                sh '''
                    echo "Deploying static site..."

                    scp -i $SSH_KEY -o StrictHostKeyChecking=no \
                        static-site/index.html \
                        $APP_USER@$APP_SERVER:/home/app-server/website/

                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no \
                        $APP_USER@$APP_SERVER \
                        "docker restart first-test"

                    echo "Deployment complete!"
                '''
            }
        }
    }
}
