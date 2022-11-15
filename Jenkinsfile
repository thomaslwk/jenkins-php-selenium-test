pipeline {
	agent none
	stages {
		stage('Setup bridge network') {
	        agent any
				steps {
				    sh 'docker network create --driver bridge my-net'
			    }
	    }
		stage('Integration UI Test') {
			parallel {
				stage('Deploy') {
					agent any
					steps {
						sh 'chmod +x ./jenkins/scripts/deploy.sh'
						sh './jenkins/scripts/deploy.sh'
						input message: 'Finished using the web site? (Click "Proceed" to continue)'
						sh 'chmod +x ./jenkins/scripts/kill.sh'
						sh './jenkins/scripts/kill.sh'
						// sh 'docker network rm my-net'
					}
				}
				// stage('Headless Browser Test') {
				// 	agent {
				// 		docker {
				// 			image 'maven:3-alpine'
				// 			args '-v /root/.m2:/root/.m2 --network my-net'
				// 		}
				// 	}
				// 	steps {
				// 		sh 'mvn -B -DskipTests clean package'
				// 		sh 'mvn test'
				// 	}
				// 	post {
				// 		always {
				// 			junit 'target/surefire-reports/*.xml'
				// 		}
				// 	}
				// }
			}
		}
		stage ('Code Quality Check via SonarQube') {
            steps {
                script {
                    def scannerHome = tool 'SonarQube';
                    withSonarQubeEnv('SonarQube') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=lab07 -Dsonar.sources=."
                    }
                }
            }
        }
	}
	 post {
        always {
            recordIssues enabledForFailure: true, tool: SonarQube()
        }
    }
}
