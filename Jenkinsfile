pipeline {
    agent { label 'worker' }
    options {
        timestamps()
        timeout(time: 15, unit: "MINUTES")
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
        stage("Trivy FS Scan") {
            steps {
                sh "trivy fs --format json -o trivy-fs-report.json ."
            }
            post {
                always {
                    archiveArtifacts artifacts: 'trivy-fs-report.json', allowEmptyArchive: true
                }
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
        stage("Trivy Image Scan") {
            steps {
                sh "trivy image --format json -o trivy-image-report.json flask-app"
            }
            post {
                always {
                    archiveArtifacts artifacts: 'trivy-image-report.json', allowEmptyArchive: true
                }
            }
        }
        stage("Run Containers") {
            steps {
                sh "docker run -d --name flask-app --network app-network flask-app"
                sh "docker run -d --name nginx-proxy --network app-network -p 80:80 nginx-proxy"
            }
        }
        stage("Unit Tests") {
            steps {
                script {
                    catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                        sh """
                            python3 -m venv .venv
                            . .venv/bin/activate
                            pip install requests
                            python3 -m unittest -v test_app.py
                            deactivate
                        """
                    }
                }
            }
        }
    }
}
