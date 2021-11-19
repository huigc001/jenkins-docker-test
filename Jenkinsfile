pipeline {
    agent any

	environment {
		dockerHome = tool 'JenkinsDocker'
		mavenHome = tool 'JenkinsMaven'
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
	}

	stages{
		stage('Checkout') {
			steps{
				slackSend message: "Pipeline Started: ${env.JOB_NAME} - ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
				sh "mvn --version"
				sh "docker version"
				echo "PATH - $PATH"
			}
		    post {
               failure {
                    script { env.FAILURE_STAGE = 'Checkout' }
                }
            }
		}

		stage('Build') {
			steps{
				sh "mvn clean compile"
				sh "mvn package -DskipTests"
			}
			
		    post {
               failure {
                    script { env.FAILURE_STAGE = 'Build' }
                }
            }
		}

		stage('Unit Test') {
			steps{
				echo "Unit-Tests, Static Code Analysis etc."
				sh "mvn test"
			}
			
		    post {
               failure {
                    script { env.FAILURE_STAGE = 'Unit Test' }
                }
            }
		}

		stage('Deploy') {
            steps{
                script{
                    sh "docker build . -t ${env.JOB_NAME}"
					sh "docker-compose up -d"
                }
            }
            
		    post {
               failure {
                    script { env.FAILURE_STAGE = 'Deploy' }
                }
            }
        }


		stage('Security Test') {
            steps{
                script{
                    echo "Dynamic Security Analysis will be done here later on !"
                }
            }
            
		    post {
               failure {
                    script { env.FAILURE_STAGE = 'Security Test' }
                }
            }
        }
	}

	post {
		success {
			slackSend message: """
            *Jenkins Build*
            Job name: `${env.JOB_NAME}`
            Build number: `#${env.BUILD_NUMBER}`
            Build status: `${currentBuild.result}`
            Author: ${sh(script: 'git show -s --format="%aN <%aE>" | awk \'{print $1 " " $2}\'', returnStdout: true).trim()}
            Build details: <${env.BUILD_URL}/console|See in web console>
			""", color: "#00FF00"
		}
		failure {
			slackSend message: """
            *Jenkins Build*
            Job name: `${env.JOB_NAME}`
            Build number: `#${env.BUILD_NUMBER}`
            Build status: `${currentBuild.result}`
            Author: ${sh(script: 'git show -s --format="%aN <%aE>" | awk \'{print $1 " " $2}\'', returnStdout: true).trim()}
            Build details: <${env.BUILD_URL}/console|See in web console>
			""", color: "#FF0000"
		}
	}
}
