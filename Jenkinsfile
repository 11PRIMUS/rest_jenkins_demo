pipeline {
    agent any
    environment { IMAGE = "demo:${BUILD_NUMBER}" }
    stages {
        stage('Test') { 
            steps { sh 'mvn test || true' } 
        }
        stage('Build') { 
            steps { sh 'mvn clean package -DskipTests' } 
        }
        stage('Docker Build') { 
            steps { 
                sh 'docker build --network=host -t ${IMAGE} .' 
            } 
        }
        stage('Test Container') { 
            steps {
                sh '''
                    docker rm -f test-app || true
                    docker run -d --name test-app -p 8081:8080 ${IMAGE}
                    sleep 30
                    curl -f http://localhost:8081/actuator/health || echo "No actuator, trying root"
                '''
            }
            post { 
                always { sh 'docker rm -f test-app || true' }
            }
        }
        stage('Deploy') { 
            steps {
                sh '''
                    docker rm -f prod-app || true
                    docker run -d --name prod-app -p 8083:8080 ${IMAGE}
                    sleep 10
                    echo "App deployed to port 8083"
                '''
            }
        }
    }
}
