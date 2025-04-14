pipeline {
    agent any

    environment {
        WEB_DIR = '/var/www/html'
        ASSETS_DIR = '/var/lib/jenkins/workspace/pikniknow-web-pipeline/assets'
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
                    // Check if Nginx is installed
                    def nginxInstalled = sh(script: 'which nginx', returnStatus: true) == 0
                    if (!nginxInstalled) {
                        echo 'Nginx is not installed. Installing...'
                        sh 'sudo apt update && sudo apt install -y nginx'
                    } else {
                        echo 'Nginx is already installed.'
                    }
                }
            }
        }

        stage('Configure Nginx Site') {
            steps {
                script {
                    // Copy Nginx configuration
                    sh 'sudo cp /var/lib/jenkins/workspace/pikniknow-web-pipeline/pikniknow-web /etc/nginx/sites-available/pikniknow-web'
                    sh 'sudo ln -sf /etc/nginx/sites-available/pikniknow-web /etc/nginx/sites-enabled/'

                    // Reload Nginx to apply the new configuration
                    sh 'sudo systemctl reload nginx'
                }
            }
        }

        stage('Deploy Website') {
            steps {
                script {
                    // Copy assets folder to /var/www/html
                    sh "sudo cp -r ${ASSETS_DIR} ${WEB_DIR}"

                    // Set correct permissions on assets
                    sh "sudo chmod -R 755 ${WEB_DIR}/assets"
                    sh "sudo chown -R www-data:www-data ${WEB_DIR}/assets"

                    // Reload Nginx to apply changes
                    sh 'sudo systemctl reload nginx'

                    echo '✅ Deployment completed!'
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            // Optional: Clean up workspace or perform any final steps
        }
    }
}
