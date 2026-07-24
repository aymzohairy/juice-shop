pipeline {
    agent any
    
    tools {
        // Must match the name you gave it in Manage Jenkins > Global Tool Configuration
        nodejs 'node26' 
    }
    
    stages {
        stage('Test') {
            options {
                // Safeguard against the Mocha test suite hanging indefinitely
                timeout(time: 10, unit: 'MINUTES')
            }
            steps {
                // Install dependencies using npm instead of yarn
                sh 'npm install'
                
                // Execute the test script defined in package.json with the memory fix applied
                // Note: The '-- --exit' syntax passes the exit flag through npm to the underlying test runner
                sh '''
                  export NODE_OPTIONS="--max-old-space-size=4096"
                  npm test -- --exit
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