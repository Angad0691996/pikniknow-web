pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/Angad0691996/pikniknow-web..git"
        NGINX_CONF = "/etc/nginx/sites-available/pikniknow"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: "${REPO_URL}", credentialsId: 'github-creds'
            }
        }

        stage('Install Nginx if Missing') {
            steps {
                script {
                    def nginxInstalled = sh(script: "command -v nginx", returnStatus: true) == 0
                    if (nginxInstalled) {
                        echo "✅ Nginx already installed."
                    } else {
                        echo "📦 Installing Nginx..."
                        sh "sudo apt update && sudo apt install -y nginx"
                    }
                    sh "sudo systemctl enable nginx"
                    sh "sudo systemctl start nginx"
                }
            }
        }

        stage('Configure Nginx Site') {
            steps {
                script {
                    echo "🔧 Setting up Nginx site config..."

                    sh """
                    sudo mkdir -p /var/www/pikniknow-web
                    sudo cp -r * /var/www/pikniknow-web/

                    cat <<EOF | sudo tee ${NGINX_CONF}
server {
    listen 80;
    server_name _;

    root /var/www/pikniknow-web;
    index index.html;

    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF
                    sudo ln -sf ${NGINX_CONF} /etc/nginx/sites-enabled/pikniknow
                    """
                }
            }
        }

        stage('Deploy Website') {
            steps {
                echo "🚀 Deploying website to /var/www/pikniknow-web"
            }
        }

        stage('Restart Nginx') {
            steps {
                sh "sudo systemctl restart nginx"
                echo "🔁 Nginx restarted"
            }
        }
    }

    post {
        failure {
            echo "❌ Deployment failed!"
        }
        success {
            echo "✅ Deployment successful!"
        }
    }
}
