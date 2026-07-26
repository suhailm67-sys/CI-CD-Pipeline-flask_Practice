pipeline {

    agent any

    environment {
        VENV = "venv"
    }

    stages {

        stage('Checkout') {

            steps {

                git branch: 'main',
                url: 'https://github.com/<yourusername>/flask_Practice.git'
            }
        }

        stage('Build') {

            steps {

                sh '''
                python3 -m venv $VENV
                . $VENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                pip install pytest
                '''
            }

        }

        stage('Test') {

            steps {

                sh '''
                . $VENV/bin/activate
                pytest
                '''
            }

        }

        stage('Deploy') {

            steps {

                sh '''
                . $VENV/bin/activate
                nohup python app.py > flask.log 2>&1 &
                '''
            }

        }

    }

    post {

        success {

            emailext (
                subject: "SUCCESS: Jenkins Build ${BUILD_NUMBER}",
                body: "Build Successful",
                to: "yourmail@gmail.com"
            )

        }

        failure {

            emailext (
                subject: "FAILED: Jenkins Build ${BUILD_NUMBER}",
                body: "Build Failed",
                to: "yourmail@gmail.com"
            )

        }

    }

}
