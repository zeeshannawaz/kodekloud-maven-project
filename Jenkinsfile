pipeline {
	agent {
		label 'maven'
	}
	tools {
		maven 'maven3'
		jdk 'jdk17'
	}
	stages {
		stage('checkout') {
			steps {
				git branch: 'main', credentialsId: 'zeeshan.nawaz275@gmail.com', url: 'https://github.com/zeeshannawaz/kodekloud-maven-project.git'
			}
		}
		stage('Build') {
			steps {
			    script {
			    		sh "${tool 'jdk17'}/bin/java --version"
			    		sh "${tool 'maven3'}/bin/mvn --version"
					    sh "${tool 'maven3'}/bin/mvn clean package -DskipTests=true"
			    }
			}
		}
		stage('Tests') {
			steps {
				sh "${tool 'maven3'}/bin/mvn test"
			}
		}
		stage('CodeCoverage') {
			steps {
			    junit 'target/surefire-reports/*.xml'
				jacoco execPattern: 'target/jacoco.exec'
			}
		}
		stage('SonarQube Analysis') {
		    steps{
		        sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numberic-project -Dsonar.host.url=http://sonarqube-service.sonar:9000 -Dsonar.login=sqp_bb9547736b1b2be7ac850884c336ab6ccbb0a8a4"
		        }
        }
        stage("Quality Gate"){
            steps {
                script {
                    timeout(time: 5, unit: 'HOURS') { 
                    	def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                    	if (qg.status != 'OK') {
                        	error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    	}
                	}
            	}
        	}
        }
		stage('PublishArtifact') {
			steps {
				archiveArtifacts artifacts: '/target/*.jar'
			}
		}
	}
}
