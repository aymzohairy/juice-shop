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
                sh 'yarn why @types/express'
                sh 'yarn why @types/express-serve-static-core'
                sh 'yarn remove @types/express-jwt'
                sh 'npx -y yarn install'
                sh 'npx tsc --noEmit -p .'
                sh 'npx -y yarn test'
            }
        }

        // stage('Build Image') {
        //     steps {
        //         // Assuming the underlying Jenkins agent has the Docker daemon running
        //         sh 'docker build -t techworldwithnana/demo-app:juice-shop-1.1 .'
        //         sh 'docker push techworldwithnana/demo-app:juice-shop-1.1'
        //     }
        // }
    }
}