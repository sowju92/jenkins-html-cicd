
pipeline {

    agent any

    triggers {
        githubPush()
    }

    environment {
        APP_DIR = '/usr/share/nginx/html'
        ARTIFACT = 'jenkins-html-app.zip'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Downloading source code from GitHub'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building HTML application'

                sh '''
                    mkdir -p build
                    cp index.html build/index.html
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Testing HTML application'

                sh '''
                    test -s build/index.html
                    grep -q "<html" build/index.html
                    grep -q "</html>" build/index.html

                    echo "HTML validation successful"
                '''
            }
        }

        stage('Create Artifact') {
            steps {
                echo 'Creating ZIP artifact'

                sh '''
                    zip -j build/${ARTIFACT} build/index.html
                '''

                archiveArtifacts(
                    artifacts: 'build/jenkins-html-app.zip',
                    fingerprint: true
                )
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application to Nginx'

                sh '''
                    install -m 644 build/index.html \
                    ${APP_DIR}/index.html
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying website deployment'

                sh '''
                    curl -f http://localhost/
                    echo "Deployment successful"
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed. Check console output.'
        }
    }
}
