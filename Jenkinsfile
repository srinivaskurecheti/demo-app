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


    }
}

