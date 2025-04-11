pipeline {
    agent any

    environment {
        DEPLOY_DIR = "/var/www/pikniknow-web"
        NGINX_CONF = "/etc/nginx/sites-available/pikniknow-web"
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

        stage('Configure Nginx Site') {
            steps {
                script {
                    sh """
                        echo "🔧 Setting up Nginx site config..."

                        sudo mkdir -p \$DEPLOY_DIR

                        sudo bash -c 'cat > \$NGINX_CONF' <<EOF
server {
    listen 80;
    server_name _;

    root \$DEPLOY_DIR;
    index index.html;

    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF

                        sudo ln -sf \$NGINX_CONF /etc/nginx/sites-enabled/pikniknow-web
                        sudo rm -f /etc/nginx/sites-enabled/default || true
                        sudo nginx -t
                    """
                }
            }
        }

        stage('Deploy Website') {
            steps {
                script {
                    sh """
                        echo "🚀 Deploying website..."

                        sudo rm -rf \$DEPLOY_DIR/*
                        sudo rsync -av --exclude='.git' --exclude='Jenkinsfile' ./ \$DEPLOY_DIR/
                        sudo chown -R www-data:www-data \$DEPLOY_DIR
                        sudo chmod -R 755 \$DEPLOY_DIR
                    """
                }
            }
        }

        stage('Restart Nginx') {
            steps {
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
