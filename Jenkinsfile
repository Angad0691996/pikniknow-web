pipeline {
    agent any
    
    environment {
        DEPLOY_DIR = '/var/www/html/pikniknow-web'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Nginx if Missing') {
            steps {
                script {
                    if (!fileExists('/etc/nginx/nginx.conf')) {
                        echo 'Nginx is not installed. Installing Nginx...'
                        sh 'sudo apt-get update && sudo apt-get install -y nginx'
                    } else {
                        echo 'Nginx is already installed.'
                    }
                }
            }
        }
        
        stage('Configure Nginx Site') {
            steps {
                script {
                    def siteConfig = '''
                    server {
                        listen 80;
                        server_name localhost;

                        root /var/www/html/pikniknow-web;
                        index index.html;

                        location / {
                            try_files $uri $uri/ =404;
                        }
                    }
                    '''
                    // Use sudo to write to the Nginx configuration directory
                    sh 'echo "${siteConfig}" | sudo tee /etc/nginx/sites-available/pikniknow-web'
                    sh 'sudo ln -s /etc/nginx/sites-available/pikniknow-web /etc/nginx/sites-enabled/'
                    sh 'sudo nginx -t'
                }
            }
        }
        
        stage('Deploy Website') {
            steps {
                echo 'Deploying HTML files to Nginx'
                sh 'sudo cp -r * /var/www/html/pikniknow-web'
                sh 'sudo chown -R www-data:www-data /var/www/html/pikniknow-web'
                sh 'sudo systemctl restart nginx'
            }
        }
    }
    
    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
