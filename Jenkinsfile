pipeline {
    agent any
    
    tools {
        // Must match the name you gave it in Manage Jenkins > Global Tool Configuration
        nodejs 'node26' 
    }
    
    stages {
        stage('Test') {
            agent {
                docker {
                    image 'node:26-alpine'
                    reuseNode true
                    args '-e HOME=/tmp'
                }
            }
           // options {
                // Safeguard against the Mocha test suite hanging indefinitely
             //   timeout(time: 30, unit: 'MINUTES')
           // }
            steps {
                sh 'npx -y yarn install'
                sh 'npx -y yarn test || true'
            }
        }

        // stage('Build Image') {
        //     steps {
        //         // Assuming the underlying Jenkins agent has the Docker daemon running
        //         sh 'docker build -t ayzohairy/demo-app:juice-shop-1.1 .'
        //         //sh 'docker push ayzohairy/demo-app:juice-shop-1.1'
        //     }
        // }

        
        stage('Secret Scan (Gitleaks)') {
            agent {
                docker {
                    image 'zricethezav/gitleaks:latest'
                    reuseNode true
                    args '--entrypoint=""'
                }
            }
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                    sh '''
                        gitleaks detect \
                        --config .gitleaks.toml \
                        --source . \
                        --report-format json \
                        --report-path gitleaks-report.json \
                        --redact \
                        --verbose \
                        --exit-code 1
                    '''
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'gitleaks-report.json', allowEmptyArchive: true
                }
            }
        }
    }

}