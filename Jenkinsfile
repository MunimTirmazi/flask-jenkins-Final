pipeline {
    agent any

    triggers {
        // For GitHub webhooks: configure "GitHub hook trigger for GITScm polling"
        pollSCM('') // keep empty when using webhooks only
    }

    environment {
        VENV_DIR = 'venv'
        TARGET_DIR = 'deploy_target'
    }

    stages {
        stage('Clone Repository') {
            steps {
                // When the pipeline is created as a "Multibranch" or "Pipeline from SCM",
                // Jenkins will do the actual clone; this echo is just for logs.
                echo "Repository checked out by Jenkins SCM"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh '''
                    . ${VENV_DIR}/bin/activate
                    pytest -q
                '''
            }
        }

        stage('Build Application') {
            steps {
                sh '''
                    mkdir -p build
                    cp -r app.py requirements.txt ${VENV_DIR} build/
                    echo "Build step completed"
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                    mkdir -p ${TARGET_DIR}
                    cp -r app.py ${TARGET_DIR}/
                    # simulate restart of a service
                    echo "Simulating service restart..."
                    ps aux | grep app.py || true
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished (success or failure).'
        }
        success {
            echo 'Flask app built, tested, and deployed (simulated) successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
