pipeline {
    agent any

	environment {
		name = "demo-service"
		dockerHome = tool 'JenkinsDocker'
		mavenHome = tool 'JenkinsMaven'
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
	}

	stages{
		stage('Checkout') {
			steps{
				slackSend message: "Pipeline Started: ${env.JOB_NAME} - ${env.BUILD_NUMBER}"
				sh "mvn --version"
				sh "docker version"
				echo "PATH - $PATH"
			}
		}

		stage('Build') {
			steps{
				sh "mvn clean compile"
				sh "mvn package -DskipTests"
			}
		}

		stage('Unit Test') {
			steps{
				echo "Unit-Tests, Static Code Analysis etc."
				sh "mvn test"
			}
		}

		stage('Deploy') {
            steps{
                script{
                    sh "docker build . -t $name"
					sh "docker-compose up -d"
                }
            }
        }


		stage('Security Test') {
            steps{
                script{
                    echo "Dynamic Security Analysis will be done here later on !"
                }
            }
        }
	}

	post {
		always{
			success {
					slackSend message: "DEPLOYED - ${env.JOB_NAME} - ${env.BUILD_NUMBER}", color: good
			}
			failure {
					slackSend message: "FAILED - ${env.JOB_NAME} - ${env.BUILD_NUMBER}", color: danger
			}
		}
	}
}
