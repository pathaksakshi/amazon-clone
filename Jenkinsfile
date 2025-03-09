pipeline {
    agent any

    environment {
        NODE_VERSION = "18"  // Set your Node.js version
    }

    stages {
        stage('Update System') {
            steps {
                script {
                    sh '''
                    sudo apt update
                    sudo apt-get install -y gnupg curl
                    '''
                }
            }
        }

        stage('Install MongoDB') {
            steps {
                script {
                    sh '''
                    curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg --dearmor
                    echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
                    sudo apt-get update
                    sudo apt-get install -y mongodb-org
                    sudo systemctl start mongod
                    sudo systemctl enable mongod
                    '''
                }
            }
        }

        stage('Install Node.js and PM2') {
            steps {
                script {
                    sh '''
                    sudo apt-get install -y npm
                    sudo npm install -g pm2
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh '''
                    npm install
                    cd client
                    npm install
                    '''
                }
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    sh '''
                    cd client
                    npm run build
                    '''
                }
            }
        }

        stage('Start Backend with PM2') {
            steps {
                script {
                    sh '''
                    pm2 delete amazon-clone || true
                    pm2 start --name amazon-clone npm -- start
                    pm2 save
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh 'pm2 list'
                }
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}
