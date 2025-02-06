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

        stage('Run SCA Analysis Using Snyk') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                        sh 'mvn snyk:test --json | snyk-to-html -o ScanResults-opensource-SCA.html'
                    }
                }
                archiveArtifacts artifacts: 'ScanResults-opensource-SCA.html', allowEmptyArchive: true
            }
        }

        stage('Run SAST Using Snyk') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                        sh 'snyk code test --json | snyk-to-html -o SNYK-SAST-ScanResults.html --token=$SNYK_TOKEN'
                    }
                }
                archiveArtifacts artifacts: 'SNYK-SAST-ScanResults.html', allowEmptyArchive: true
            }
        }
    }
}
