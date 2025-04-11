pipeline {
    agent any

    environment {
        DEPLOY_DIR = "/var/www/pikniknow-web"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-credentials', url: 'https://github.com/Angad0691996/pikniknow-web.git', branch: 'main'
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
                        echo "📝 Writing Nginx site config..."
                        cat <<EOF | sudo tee /etc/nginx/sites-available/pikniknow-web
server {
    listen 80;
    server_name _;

    root $DEPLOY_DIR;
    index index.html;

    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF
                        echo "🔗 Enabling site and disabling default..."
                        sudo ln -sf /etc/nginx/sites-available/pikniknow-web /etc/ng
