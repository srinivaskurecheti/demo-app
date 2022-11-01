pipeline {

    agent any

    tools {
        maven "maven"
    }

    stages {

        stage('Git checkout') {

            steps {
                git branch: 'main', url: 'https://github.com/srinivaskurecheti/demo-app.git'
            }
        }

	stage('Unit Testing') {
            steps {
               sh 'mvn test'
            }
	    post {
                always {
                    junit '**/target/surefire-reports/TEST-mypackage.CalculatorTest.xml'
		}

	      }
        }


	 stage('Building') {
            steps {
               sh 'mvn clean package'
            }
        }


    }
}

