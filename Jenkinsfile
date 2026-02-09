pipeline {
    agent any

    options {
        skipDefaultCheckout(true)   // 👈 IMPORTANT
    }

    parameters {
        string(name: 'BUILD_TYPE', defaultValue: '', description: 'Detected build type')
    }

    stages {

        stage('Checkout') {
            steps {
                cleanWs()          // 👈 clean old junk (venv, __pycache__, etc.)
                checkout scm       // 👈 clone repo ONCE, clean
            }
        }

        stage('Discovery') {
            steps {
                sh '''
                echo "Repository structure:"
                tree -L 4 || ls -R
                tree -L 4 > structure.txt || ls -R > structure.txt
                '''
            }
        }

        stage('Build') {
            when {
                expression { params.BUILD_TYPE == 'python' }
            }
            steps {
                sh '''
                python3 --version
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                pip install -e .
                pytest
                echo "Successfully tested!"
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'structure.txt', fingerprint: true
        }
    }
}
