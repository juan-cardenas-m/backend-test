pipeline {
    agent any
    stages {
        stage('Instalaciones necesarias (preliminar)') {
			agent{
				docker{
					image 'node:22'
					reuseNode true
				}
			}
            stages {
				stage('Dependencias'){
					steps{
						sh 'npm install'
					}
				}
				stage('Testing y cobertura') {
					steps {
						sh 'npm run test:cov'
					}
				}
				stage('Build') {
					steps {
						sh 'npm run build'
					}
				}
			}
		}
		stage('Etapa QA Sonarqube'){
			agent{
				docker{
					image 'sonarsource/sonar-scanner-cli'
					args '--network devops-infra_default'
					reuseNode true
				}
			}
			stages{
				stage('Upload a Sonarqube'){
					steps{
						withSonarQubeEnv('SonarQube'){
							sh 'sonar-scanner'
						}
					}
				}
			}
		}
		stage('Etapa Empaquetado y Delivery'){
			steps{
				script{
					docker.withRegistry('http://localhost:8082', 'nexus-credentials'){	
						sh 'docker tag backend-test localhost:8082/backend-test:ftr'
						sh 'docker push localhost:8082/backend-test:ftr'
					}
				}
			}
		}
	}
}