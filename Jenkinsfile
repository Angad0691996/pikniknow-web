pipeline {
    agent any

    environment {
        NODE_ENV = 'production'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Angad0691996/pikniknow-web.git', branch: 'main', credentialsId: 'github-creds'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '📦 Installing Node and npm...'
                sh '''
                    curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
                    sudo apt-get install -y nodejs
                '''
            }
        }

        stage('Build React App') {
            steps {
                echo '🛠️ Building React frontend...'
                dir('pikniknow-web') {
                    sh '''
                        npm install
                        npm run build
                    '''
                }
            }
        }

        stage('Install Nginx if Missing') {
            steps {
                echo '🌐 Installing Nginx if not installed...'
                sh '''
                    if ! which nginx > /dev/null; then
                        sudo apt-get update
                        sudo apt-get install -y nginx
                    fi
                '''
            }
        }

        stage('Configure Nginx Site') {
            steps {
                echo '⚙️ Configuring Nginx site...'
                sh '''
                    sudo rm -rf /var/www/html/*
                    sudo cp -r pikniknow-web/build/* /var/www/html/
                    sudo chown -R www-data:www-data /var/www/html
                '''
            }
        }

        stage('Restart Nginx') {
            steps {
                echo '🔄 Restarting Nginx...'
                sh 'sudo systemctl restart nginx'
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
