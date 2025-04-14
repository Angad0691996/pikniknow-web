pipeline {
    agent any

    environment {
        // Define environment variables here if needed
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "📦 Installing Node and npm..."
                sh 'curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -'
                sh 'sudo apt-get install -y nodejs'
            }
        }

        stage('Build React App') {
            steps {
                echo "🛠️ Building React frontend..."
                dir('pikniknow-web') {
                    sh 'npm install'
                }
            }
        }

        stage('Install Nginx if Missing') {
            steps {
                echo "Installing Nginx if missing..."
                sh 'sudo apt-get install -y nginx'
            }
        }

        stage('Configure Nginx Site') {
            steps {
                echo "Configuring Nginx site..."
                // Add Nginx configuration steps here
            }
        }

        stage('Restart Nginx') {
            steps {
                echo "Restarting Nginx..."
                sh 'sudo systemctl restart nginx'
            }
        }
    }

    post {
        always {
            echo "❌ Deployment finished!"
        }

        success {
            echo "✅ Deployment successful!"
        }

        failure {
            echo "❌ Deployment failed!"
        }
    }
}
