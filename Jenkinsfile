pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out PDF Navigator Extension'

                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/MMounika-DevOps/pdf-navigator-extension.git'
                    ]]
                ])
            }
        }

        stage('Verify Source') {
            steps {
                echo 'Verifying source files'

                sh '''
                    pwd
                    ls -la

                    test -f manifest.json
                    test -f popup.html
                    test -f popup.css
                    test -f popup.js
                    test -f Dockerfile
                    test -f nginx.conf
                    test -f docker-compose.yml
                    test -f Jenkinsfile

                    echo "Source verification successful"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'

                sh '''
                    docker build -t pdf-navigator:latest .
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Deploying PDF Navigator application'

                sh '''
                    docker rm -f pdf-navigator || true

                    docker run -d \
                        --name pdf-navigator \
                        -p 80:80 \
                        --restart unless-stopped \
                        pdf-navigator:latest
                '''
            }
        }

        stage('Verify Container') {
            steps {
                echo 'Verifying Docker container'

                sh '''
                    docker ps
                    docker inspect pdf-navigator
                '''
            }
        }

        stage('Application Test') {
            steps {
                echo 'Testing application'

                sh '''
                    sleep 5
                    curl -f http://localhost/
                    echo
                    echo "Application test successful"
                '''
            }
        }
    }

    post {
        success {
            echo '========================================='
            echo 'PDF Navigator deployment SUCCESSFUL'
            echo '========================================='
        }

        failure {
            echo '========================================='
            echo 'PDF Navigator deployment FAILED'
            echo '========================================='
        }
    }
}
