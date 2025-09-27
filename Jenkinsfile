pipeline {
    agent any 

    environment {
        VENV_DIR = 'venv'
        GCP_PROJECT = 'gen-lang-client-0792134177'
        GCLOUD_PATH = '/var/jenkins_home/google-cloud/sdk/bin'
    }

    stages {

        stage('Cloning Github repo to Jenkins') {
            steps {
                script {
                    echo 'Cloning Github repo to Jenkins..............'
                    checkout([$class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[
                            credentialsId: 'github-token',
                            url: 'https://github.com/Amitgitup/Hotel-Reservation-Prediction'
                        ]]
                    ])
                }
            }
        }

        stage('Setting up Virtual Environment and installing dependencies') {
            steps {
                script {
                    echo 'Setting up Virtual Environment and installing dependencies...........'
                    sh '''
                    python -m venv ${VENV_DIR}
                    source ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -e .
                    '''
                }
            }
        }

        stage('Building and Pushing Docker Image to GCR') {
            steps {
                withCredentials([file(credentialsId: 'gcp-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    script {
                        echo 'Building and Pushing Docker Image to GCR..........'
                        sh '''
                        # Add gcloud SDK to PATH
                        export PATH=$PATH:${GCLOUD_PATH}

                        # Authenticate with GCP
                        gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                        gcloud config set project ${GCP_PROJECT}
                        gcloud auth configure-docker --quiet

                        # Build and push Docker image
                        docker build -t gcr.io/${GCP_PROJECT}/ml-project:latest .
                        docker push gcr.io/${GCP_PROJECT}/ml-project:latest
                        '''
                    }
                }
            }
        }

    }
}
