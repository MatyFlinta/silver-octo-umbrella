pipeline {
    agent { label 'worker' }
    options {
        timestamps()
        timeout(time: 10, unit: "MINUTES")
    }
    stages {
        stage("Clean Up") {
            steps {
                sh """
                    docker stop flask-app nginx-proxy || true
                    docker rm flask-app nginx-proxy || true
                    docker network rm app-network || true
                """
            }
        }
        stage("Setup") {
            steps {
                sh "docker network create app-network"
            }
        }
        stage("Build Images") {
            steps {
                sh "docker build -t flask-app ./Task1"
                sh "docker build -t nginx-proxy ./Task1/nginx-proxy"
            }
        }
        stage("Run Containers") {
            steps {
                sh "docker run -d --name flask-app --network app-network flask-app"
                sh "docker run -d --name nginx-proxy --network app-network -p 80:80 nginx-proxy"
            }
        }
    }
}
