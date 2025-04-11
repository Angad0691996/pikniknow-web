pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/Angad0691996/pikniknow-web.git"
        DEPLOY_DIR = "/var/www/pikniknow-web"
        NGINX_CONF = "/etc/nginx/sites-available/pikniknow-web"
        NGINX_SITE_LINK = "/etc/nginx/sites-enabled/pikniknow-web"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-credentials', url: "${REPO_URL}", branch: 'main'
            }
        }

        stage('Install Nginx if Missing') {
            steps {
                script {
                    sh '''
                        if ! command -v nginx >/dev/null; then
                            echo 🛠️ Installing Nginx...
                            sudo apt update
                            sudo apt install -y nginx
                        else
                            echo ✅ Nginx already installed.
                        fi

                        sudo systemctl enable nginx
                        sudo systemctl start nginx
                    '''
                }
            }
        }

        stage('Configure Nginx Site') {
            steps {
                script {
                    sh '''
                        echo 🔧 Setting up Nginx site config...
                        sudo mkdir -p ${DEPLOY_DIR}

                        sudo bash -c 'cat > ${NGINX_CONF}' <<EOF
server {
    listen 80;
    server_name _;

    root ${DEPLOY_DIR};
    index index.html;

    location / {
        try_files \\$uri \\$uri/ =404;
    }
}
EOF

                        sudo ln -sf ${NGINX_CONF} ${NGINX_SITE_LINK}
                    '''
                }
            }
        }

        stage('Deploy Website') {
            steps {
                script {
                    sh '''
                        echo 📂 Copying website files to ${DEPLOY_DIR}...
                        sudo cp -r * ${DEPLOY_DIR}
                        sudo chown -R www-data:www-data ${DEPLOY_DIR}
                    '''
                }
            }
        }

        stage('Restart Nginx') {
            steps {
                script {
                    sh '''
                        echo 🔄 Restarting Nginx...
                        sudo nginx -t && sudo systemctl restart nginx
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
