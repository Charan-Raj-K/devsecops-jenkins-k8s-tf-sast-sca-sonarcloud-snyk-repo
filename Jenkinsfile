pipeline {
  agent any
  tools { 
        maven 'Maven-3.2.5'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=devsecops-2025 -Dsonar.organization=devsecops-2025 -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=0514d8b1a300bd18eb54abb3da1b9ff3e2d98e4b'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test --json | snyk-to-html -o ScanResults-opensource-SCA.html --fn'
				}
			}
    }
	stage('RunSASTUsingSNYK'){
		steps {
			withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'snyk code test --json | snyk-to-html -o SNYK-SAST-ScanResults.html --fn --token=$SNYK_TOKEN'
				}
		}
	}
  }
}
