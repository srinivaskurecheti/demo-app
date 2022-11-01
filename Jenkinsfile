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
                    junit '**/target/surefire-reports/TEST-com.example.springboot.SpringbootApplicationTests.xml'
		}

	      }
        }


	 stage('Building') {
            steps {
               sh 'mvn clean package'
            }
        }


	stage('Sonarqube analysis') {
            steps {

	        script{

               withSonarQubeEnv(credentialsId: 'sonar') {
	       sh 'mvn clean package sonar:sonar'
                    }

	       }
            }
        }


	stage('Quality Gate analysis') {
            steps {

                script{

                 waitForQualityGate abortPipeline: false, credentialsId: 'sonar'

               }
            }
        }


	 stage('Artifact uploading') {
            steps {

                script{

                 nexusArtifactUploader artifacts: [[artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']], credentialsId: 'nexus', groupId: 'com.example', nexusUrl: '3.110.94.56:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'srinivas', version: '1.0.0'

               }
            }
        }


    }
}

