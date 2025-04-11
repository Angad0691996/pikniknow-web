pipeline {
    agent any

    environment {
        DEPLOY_DIR = "/var/www/pikniknow-web"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-credentials', url: 'https://github.com/Angad0691996/pikniknow-web..git', branch: 'main'
            }
        }

        stage('Install Nginx if Missing') {
            steps {
                script {
                    sh """
                        if ! command -v nginx > /dev/null; then
                            echo "🔧 Installing Nginx..."
                            if [ -f /etc/debian_version ]; then
                                sudo apt update && sudo apt install nginx -y
                            elif [ -f /etc/redhat-release ]; then
                                sudo yum install nginx -y
                            fi
                        else
                            echo "✅ Nginx already installed."
                        fi
                        sudo systemctl enable nginx
                        sudo systemctl start nginx
                    """
                }
            }
        }

        stage('Deploy Website') {
            steps {
                script {
                    sh """
                        echo "Ensuring deploy directory exists..."
                        sudo mkdir -p \$DEPLOY_DIR

                        echo "Clearing old site..."
                        sudo rm -rf \$DEPLOY_DIR/*

                        echo "Deploying new site..."
                        sudo rsync -av --exclude='.git' --exclude='Jenkinsfile' ./ \$DEPLOY_DIR/

                        sudo chown -R www-data:www-data \$DEPLOY_DIR
                    """
                }
            }
        }

        stage('Restart Nginx') {
            steps {
                script {
                    sh "sudo systemctl restart nginx"
                }
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
