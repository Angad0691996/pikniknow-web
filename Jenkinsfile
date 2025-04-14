stage('Build React App') {
    steps {
        echo "🛠️ Building React frontend..."
        sh """
            cd pikniknow-web
            npm install
            npm run build
        """
    }
}

stage('Configure Nginx Site') {
    steps {
        echo "🔧 Configuring Nginx site..."
        sh """
            sudo mkdir -p ${DEPLOY_DIR}
            sudo cp -r pikniknow-web/build/* ${DEPLOY_DIR}/
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
