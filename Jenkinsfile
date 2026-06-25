pipeline{
    agent any
    options{
        timestamps()
        timeout(time: 5, unit: "MINUTES")
    }
    stages{
        stage("make directory"){
            options{
                retry(2)
            }
            steps{
                sh """
                    if [! -d jenkins-test]; then 
                    mkdir jenkins-test
                    else
                    echo 'already exists'
                    fi
                """
            }
        }
        stage("add a file"){
            steps{
                sh "touch jenkins-test/test.txt"
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'jenkins-test/*.txt', allowEmptyArchive: true 
        }
    }
}
