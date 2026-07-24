pipeline {
    agent any

    stages {
        stage('Test') {
            agent {
                docker {
                    image 'node:26-bullseye'
                    // reuseNode true is optional but recommended to avoid workspace hopping
                    reuseNode true 
                }
            }
            steps {
                sh 'yarn install'
                sh 'yarn test'
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