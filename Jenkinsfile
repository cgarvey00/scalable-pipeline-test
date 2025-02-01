pipeline {
    agent any

    environment{
        IMAGE_NAME='cgarvey2k25/scalable-pipeline-test:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/cgarvey00/scalable-pipeline-test.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Build & Push') {
            steps {
            script {
                docker.build("${IMAGE_NAME}")
                docker.withRegistry('https://index.docker.io/v1/','docker-hub-credentials'){
                sh 'docker push ${IMAGE_NAME}'
               }
            }
        }
    }
         stage('Deploy'){
            steps{
              script{
              sh '''
              if [ "$(docker ps -q -f name=scalable-pipeline-test)"]; then
                  docker stop scalable-pipeline-test && docker rm scalable-pipeline-test
              fi
              '''

              sh "docker run -d --name scalable-pipeline-test -p 3000:3000 ${IMAGE_NAME}"
              }
            }
         }
      }


    post {
        always {
            // Ensures the workspace is clear
            cleanWs()
        }
    }
}
