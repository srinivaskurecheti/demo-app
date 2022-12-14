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

               withSonarQubeEnv(credentialsId: 'token') {
	       sh 'mvn clean package sonar:sonar'
                    }

	       }
            }
        }


	stage('Quality Gate analysis') {
            steps {

                script{

                 waitForQualityGate abortPipeline: false, credentialsId: 'token'

               }
            }
        }


	 stage('Artifact uploading') {
            steps {

                script{

                 def readPomVersion = readMavenPom file: 'pom.xml'
		 def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "srinivas"
                 nexusArtifactUploader artifacts: 
		 [
		    [artifactId: 'springboot', 
		    classifier: '', 
		    file: 'target/Uber.jar', 
		    type: 'jar'
		      ]
		         ], 
	         credentialsId: 'nexus', 
		 groupId: 'com.example', 
		 nexusUrl: '3.110.130.99:8081', 
		 nexusVersion: 'nexus3', 
		 protocol: 'http', 
		 repository: nexusRepo, 
		 version: "${readPomVersion.version}"

               }
            }
        }


	stage('Building Docker Image') {
            steps {

                script{

                 sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                 sh 'docker image tag $JOB_NAME:v1.$BUILD_ID srinivaskurecheti/$JOB_NAME:v1.$BUILD_ID'
                 sh 'docker image tag $JOB_NAME:v1.$BUILD_ID srinivaskurecheti/$JOB_NAME:latest'

               }
            }
        }


	 stage('Push the Image') {
            steps {

                script{

                   withCredentials([string(credentialsId: 'dockerhub', variable: 'Docker')]) {

                    sh 'docker login -u srinivaskurecheti -p ${Docker}'
		    sh 'docker image push srinivaskurecheti/$JOB_NAME:v1.$BUILD_ID'
		    sh 'docker image push  srinivaskurecheti/$JOB_NAME:latest'


		 }

               }
            }
        }



	stage('Kubernetes deploying') {
            steps {

                script{

                 kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")

               }
            }
        }





    }
}

