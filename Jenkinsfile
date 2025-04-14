pipeline {
    agent any

    environment {
        NGINX_SITE_CONF = '/etc/nginx/sites-available/pikniknow-web'
        NGINX_ENABLED_LINK = '/etc/nginx/sites-enabled/pikniknow-web'
        TEMP_CONF = 'pikniknow-temp.conf'
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
                    def nginxInstalled = sh(script: 'which nginx', returnStatus: true)
                    if (nginxInstalled != 0) {
                        echo 'Nginx is not installed, installing...'
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
                    // Write config to workspace (Jenkins has permission here)
                    writeFile file: TEMP_CONF, text: '''
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}
                    '''.trim()

                    // Move config with sudo
                    sh """
                        sudo cp ${TEMP_CONF} ${NGINX_SITE_CONF}
                        if [ ! -L ${NGINX_ENABLED_LINK} ]; then
                            sudo ln -s ${NGINX_SITE_CONF} ${NGINX_ENABLED_LINK}
                        else
                            echo 'Symbolic link already exists, skipping creation.'
                        fi
                    """
                }
            }
        }

        stage('Deploy Website') {
            steps {
                sh 'sudo systemctl reload nginx'
                echo '✅ Deployment completed!'
            }
        }
    }

    post {
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
