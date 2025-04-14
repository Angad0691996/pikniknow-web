pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/Angad0691996/pikniknow-web.git"
        NGINX_CONF = "/etc/nginx/sites-available/pikniknow"
        DEPLOY_DIR = "/var/www/pikniknow-web"
        REPO_DIR = "pikniknow-web"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: "${REPO_URL}", credentialsId: 'github-creds'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "📦 Installing Node and npm..."
                sh """
                    curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
                    sudo apt-get install -y nodejs
                """
            }
        }

        stage('Build React App') {
            steps {
                echo "🛠️ Building React frontend..."
                sh """
                    cd ${REPO_DIR}
                    npm install
                    npm run build
                """
            }
        }

        stage('Install Nginx if Missing') {
            steps {
                script {
                    def nginxInstalled = sh(script: "command -v nginx", returnStatus: true) == 0
                    if (!nginxInstalled) {
                        sh "sudo apt update && sudo apt install -y nginx"
                    }
                    sh "sudo systemctl enable nginx"
                    sh "sudo systemctl start nginx"
                }
            }
        }

        stage('Configure Nginx Site') {
            steps {
                echo "🔧 Configuring Nginx site..."
                sh """
                    sudo mkdir -p ${DEPLOY_DIR}
                    sudo cp -r ${REPO_DIR}/build/* ${DEPLOY_DIR}/
                    sudo chown -R www-data:www-data ${DEPLOY_DIR}
                    sudo chmod -R 755 ${DEPLOY_DIR}

                    cat <<EOF | sudo tee ${NGINX_CONF}
server {
    listen 80;
    server_name _;

    root ${DEPLOY_DIR};
    index index.html;

    location / {
        try_files \$uri /index.html;
    }
}
EOF

                    sudo ln -sf ${NGINX_CONF} /etc/nginx/sites-enabled/pikniknow
                    sudo rm -f /etc/nginx/sites-enabled/default
                """
            }
        }

        stage('Restart Nginx') {
            steps {
                sh "sudo nginx -t"
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
