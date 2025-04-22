pipeline {
    agent { label 'pikniknow' }

    environment {
        SITE_DIR = '/var/www/html'
    }

    options {
        timestamps()
        skipDefaultCheckout()
    }

    stages {
        stage("🧹 Clean Old Deployment") {
            steps {
                echo "Removing existing files from $SITE_DIR"
                sh 'sudo rm -rf $SITE_DIR/*'
            }
        }

        stage("📥 Clone GitHub Repo") {
            steps {
                echo "Cloning the latest static site code from GitHub"
                git credentialsId: 'github-creds', url: 'https://github.com/Angad0691996/pikniknow-web.git', branch: 'main'
            }
        }

        stage("🚚 Deploy Static Website") {
            steps {
                echo "Deploying static files to $SITE_DIR"
                sh '''
                sudo mkdir -p $SITE_DIR
                sudo cp -r * $SITE_DIR/
                sudo chown -R www-data:www-data $SITE_DIR
                sudo systemctl restart nginx
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment completed successfully! Visit http://43.204.228.77/"
        }
        failure {
            echo "❌ Deployment failed. Check console output for details."
        }
    }
}
