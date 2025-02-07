pipeline {
    agent any
    tools {
        maven 'Maven-3.2.5'
    }
    stages {
        stage('Compile and Run Sonar Analysis') {
            steps {
                sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=devsecops-2025 -Dsonar.organization=devsecops-2025 -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=0514d8b1a300bd18eb54abb3da1b9ff3e2d98e4b'
            }
        }

        // Build the project using Maven
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package' 
            }
        }

        stage('Run SCA Analysis Using Snyk') {
    steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
            withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                // Run Snyk test and save output to a JSON file
                sh '''
                    echo "Running Snyk SCA Scan..."
                    snyk test --json-file-output=ScanResults-opensource-SCA.json
                    echo "Checking if JSON file was created..."
                    ls -l ScanResults-opensource-SCA.json
                    echo "Converting JSON to HTML..."
                    snyk-to-html -i ScanResults-opensource-SCA.json -o ScanResults-opensource-SCA.html
                    echo "Checking if HTML file was created..."
                    ls -l ScanResults-opensource-SCA.html
                '''
            }
        }
        archiveArtifacts artifacts: 'ScanResults-opensource-SCA.html', allowEmptyArchive: true
    }
}

        stage('Run SAST Using Snyk') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                       // Run Snyk test and save output to a JSON file
                sh '''
                    echo "Running Snyk SAST Scan..."
                    snyk code test --json-file-output=SNYK-SAST-ScanResults.json
                    echo "Checking if JSON file was created..."
                    ls -l SNYK-SAST-ScanResults.json
                    echo "Converting JSON to HTML..."
                    snyk-to-html -i SNYK-SAST-ScanResults.json -o SNYK-SAST-ScanResults.html
                    echo "Checking if HTML file was created..."
                    ls -l SNYK-SAST-ScanResults.html
                '''
                   }
                }
                archiveArtifacts artifacts: 'SNYK-SAST-ScanResults.html', allowEmptyArchive: true
            }
        }
    }
}
