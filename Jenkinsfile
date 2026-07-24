pipeline {
    agent any
    tools {
        // Must match the name you gave it in Global Tool Configuration
        nodejs 'node26' 
    }
    stages {
        stage('Test') {
            steps {
                sh '''
                    export NODE_OPTIONS="--max-old-space-size=4096"
                    yarn install --frozen-lockfile --prefer-offline
                    yarn test --exit
                '''
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