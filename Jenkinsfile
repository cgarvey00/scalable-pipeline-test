pipeline {
    agent any

    environment{
        JAVA_HOME='/opt/jdk-21.0.2+13'
        PATH="${JAVA_HOME}/bin:${PATH}"
        IMAGE_NAME='cgarvey2k25/scalable-pipeline-test:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/cgarvey00/scalable-pipeline-test.git'
            }
        }

        stage('Debug Java'){
            steps{
                sh 'java -version'
                sh 'mvn -version'
            }
        }

        stage('Build') {
            steps {
                sh 'export JAVA_HOME=/opt/jdk-21.0.2+13 && mvn clean install --debug'
            }
        }

        stage('Test') {
            steps {
                sh 'export JAVA_HOME=/opt/jdk-21.0.2+13 && mvn test'
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh '''
                docker build -t "${IMAGE_NAME}" .
                echo "DOCKER_HUB_PASSWORD" | docker login -u "$DOCKER_HUB_USERNAME" --password-stdin
                docker push ${IMAGE_NAME}
                '''
            }
        }

            stage('Deploy') {
                     steps {
                         script {
                             sh '''
                             if [ "$(docker ps -q -f name=scalable-pipeline-test)" ]; then
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
