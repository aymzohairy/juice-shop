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
                    args '-e HOME=/tmp -e NPM_CONFIG_LEGACY_PEER_DEPS=true'
                }
            }
            options {
                timeout(time: 30, unit: 'MINUTES')
            }
            steps {
                sh '''
                    mkdir -p /tmp/npm-global
                    npm config set prefix '/tmp/npm-global'
                    export PATH="/tmp/npm-global/bin:$PATH"
                    npm install -g corepack
                    corepack enable
                    yarn config set cacheFolder .yarn/cache
                    yarn install --frozen-lockfile
                    yarn test || true
                '''
            }
        }

        // stage('Build Image') {
        //     steps {
        //         // Assuming the underlying Jenkins agent has the Docker daemon running
        //         sh 'docker build -t ayzohairy/demo-app:juice-shop-1.1 .'
        //         //sh 'docker push ayzohairy/demo-app:juice-shop-1.1'
        //     }
        // }

        
        // stage('Secret Scan (Gitleaks)') {
        //     agent {
        //         docker {
        //             image 'zricethezav/gitleaks:latest'
        //             reuseNode true
        //             args '--entrypoint=""'
        //         }
        //     }
        //     steps {
        //         catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
        //             sh '''
        //                 gitleaks detect \
        //                 --config .gitleaks.toml \
        //                 --source . \
        //                 --report-format json \
        //                 --report-path gitleaks-report.json \
        //                 --redact \
        //                 --verbose \
        //                 --exit-code 1
        //             '''
        //         }
        //     }
        //     post {
        //         always {
        //             archiveArtifacts artifacts: 'gitleaks-report.json', allowEmptyArchive: true
        //         }
        //     }
        // }
        // stage('SAST - njsscan') {
        //     agent {
        //         docker {
        //             image 'python:3.11-slim'
        //             reuseNode true
        //             args '-u 0:0'
        //         }
        //     }
        //     steps {
        //         sh '''
        //             pip install --no-cache-dir njsscan
        //             njsscan . --sarif -o njsscan-results.sarif || true
        //         '''
        //         archiveArtifacts artifacts: 'njsscan-results.json', allowEmptyArchive: true
        //     }
        // }
        stage('Semgrep') {
                agent {
                    docker {
                        image 'returntocorp/semgrep'
                        reuseNode true
                        // Overrides the container's default entrypoint so Jenkins can execute shell steps
                        args '--entrypoint="" -e HOME=/tmp -v "${WORKSPACE}:/src" -w /src'
                    }
                }
                environment {
                    SEMGREP_RULES = 'p/javascript'
                }
                steps {
                    // Replicates GitLab's 'allow_failure: true'
                    catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                        sh 'semgrep scan --config "${SEMGREP_RULES}" --json --json-output semgrep-result.json'
                        archiveArtifacts artifacts: 'semgrep-result.json', allowEmptyArchive: true
                    }
                }
            }       
        }

        stage('SCA - Retire.js') {
            agent {
                docker {
                    image 'node:26-alpine'
                    reuseNode true
                    // Set HOME to /tmp to avoid permission errors when running as non-root
                    args '-e HOME=/tmp'
                }
            }
            steps {
                // Run retire via npx to avoid global npm install permission issues
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    sh '''
                        npx -y retire \
                            --path node_modules \
                            --outputformat json \
                            --outputpath retire-report.json \
                            --exitwith 13
                    '''
                }
                archiveArtifacts artifacts: 'retire-report.json', allowEmptyArchive: true
            }
        }
    }
}