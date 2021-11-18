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
				slackSend message: 'Checking out...'
				sh "mvn --version"
				sh "docker version"
				echo "PATH - $PATH"
			}
		}

		stage('Compile') {
			steps{
				slackSend message: 'Compiling...'
				sh "mvn clean compile"
			}
		}

		stage('Test') {
			steps{
				slackSend message: 'Testing...'
				sh "mvn test"
			}
		}

		stage('Integration Test') {
			steps{
				slackSend message: 'Integration Testing...'
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}

		stage('Package') {
			steps{
				script{
					slackSend message: 'Packaging...'
					sh "mvn package -DskipTests"
				}
			}
		}

		stage('Build Docker Image') {
            steps{
                script{
					slackSend message: 'Building Docker Image...'
					sh "pwd"
                    sh "docker build . -t demo-app"
                }
            }
        }
	}
}
