pipeline {
    agent any
    environment { IMAGE = "demo:${BUILD_NUMBER}" }
    triggers { githubPush() }
    stages {
        stage('Test') { steps { sh 'mvn test' } }
        stage('Build') { steps { sh 'mvn package -DskipTests' } }
        stage('Docker') { steps { sh 'docker build -t ${IMAGE} .' } }
        stage('Test Container') {
            steps { sh 'docker run -d --name test -p 8081:8080 ${IMAGE} && sleep 30 && curl -f http://localhost:8081/health' }
            post { always { sh 'docker rm -f test || true' } }
        }
        stage('Deploy') { steps { sh 'docker rm -f app || true && docker run -d --name app -p 8082:8080 ${IMAGE}' } }
    }
}
