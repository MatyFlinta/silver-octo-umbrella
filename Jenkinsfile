pipeline {
    agent any
    options {
        timestamps()
        timeout(time: 5, unit: "MINUTES")
    }
    stages {
        stage("Build") {
            steps {
                echo "Starting Build stage..."
                sh "pwd"
                sh "ls"
                sh """
                    if [ ! -d jenkins-test ]; then
                        mkdir jenkins-test
                    else
                        echo 'Directory already exists'
                    fi
                """
                sh "touch jenkins-test/build-output.txt"
                echo "Build stage complete."
            }
        }
        stage("Test") {
            steps {
                echo "Starting Test stage..."
                sh "pwd"
                sh "ls jenkins-test"
                sh "touch jenkins-test/test-results.txt"
                echo "Test stage complete."
            }
        }
        stage("Deploy") {
            steps {
                echo "Starting Deploy stage..."
                sh "pwd"
                sh "mv jenkins-test/build-output.txt jenkins-test/deployed-artifact.txt"
                sh "ls jenkins-test"
                echo "Deploy stage complete."
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'jenkins-test/*.txt', allowEmptyArchive: true
        }
    }
}
