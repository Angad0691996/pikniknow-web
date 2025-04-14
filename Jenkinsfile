pipeline {
    agent any

    environment {
        // You can specify your environment variables here if needed
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                // Checkout the repository
                checkout scm
            }
        }

        stage('Install Nginx if Missing') {
            steps {
                script {
                    // Check if Nginx is installed
                    sh 'which nginx || sudo apt-get install -y nginx'
                }
            }
        }

        stage('Configure Nginx Site') {
            steps {
                script {
                    // Copy Nginx configuration (with recursive flag)
                    sh 'sudo cp -r /var/lib/jenkins/workspace/pikniknow-web-pipeline/pikniknow-web /etc/nginx/sites-available/pikniknow-web'

                    // Create the symlink in the sites-enabled directory
                    sh 'sudo ln -sf /etc/nginx/sites-available/pikniknow-web /etc/nginx/sites-enabled/'

                    // Reload Nginx to apply the new configuration
                    sh 'sudo systemctl reload nginx'
                }
            }
        }

        stage('Deploy Website') {
            steps {
                script {
                    // Copy the website content to the web root
                    sh 'sudo cp -r /var/lib/jenkins/workspace/pikniknow-web-pipeline/* /var/www/html/'

                    // Set proper permissions for web assets
                    sh 'sudo chmod -R 755 /var/www/html'
                }
            }
        }

        stage('Declarative: Post Actions') {
            steps {
                echo 'Cleaning up...'
                // Any post-action tasks can be added here
            }
        }
    }

    post {
        always {
            // Cleanup actions after pipeline execution (optional)
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'Deployment was successful!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
