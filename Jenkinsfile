pipeline {
    agent any

    environment {
        NGINX_CONF_PATH = "/etc/nginx/sites-available/pikniknow-web"
        NGINX_LINK_PATH = "/etc/nginx/sites-enabled/pikniknow-web"
        WEB_ROOT = "/var/www/html"
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
                    def nginxInstalled = sh(script: 'which nginx', returnStatus: true) == 0
                    if (!nginxInstalled) {
                        echo "Installing Nginx..."
                        sh 'sudo apt update && sudo apt install -y nginx'
                    } else {
                        echo "Nginx is already installed."
                    }
                }
            }
        }

        stage('Configure Nginx Site') {
            steps {
                script {
                    def nginxConf = """
                    server {
                        listen 80;
                        server_name _;

                        location / {
                            root ${env.WEB_ROOT};
                            index index.html index.htm;
                        }
                    }
                    """.stripIndent()

                    writeFile file: 'pikniknow-temp.conf', text: nginxConf

                    sh """
                        sudo cp pikniknow-temp.conf ${env.NGINX_CONF_PATH}
                        if [ ! -L ${env.NGINX_LINK_PATH} ]; then
                            sudo ln -s ${env.NGINX_CONF_PATH} ${env.NGINX_LINK_PATH}
                        else
                            echo "Symbolic link already exists, skipping creation."
                        fi
                    """
                }
            }
        }

        stage('Deploy Website') {
            steps {
                script {
                    sh 'sudo systemctl reload nginx'
                    echo "✅ Deployment completed!"
                }
            }
        }
    }

    post {
        failure {
            echo "❌ Deployment failed!"
        }
    }
}
