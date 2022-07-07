pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                dir("/app/") {
                    sh 'docker build -t shadow .'
                }
            }
        }
        stage('Prepare Environement') {
            steps
            {
                script {
                    containerName = sh(returnStdout: true, script: "docker ps -a -f 'name=shadow1' --format '{{.Names}}'").trim()
                    if(containerName == "shadow1")
                    {
                        sh 'docker rm shadow1 --force'
                        sh "echo 'Nettoyage environnement OK'"
                    }
                    else
                    {
                        sh "echo 'Ennvironnement OK'"
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 8090:80 --name shadow:2.0.2 shadow1 '
                sh 'sleep 15s'
            }
        }
        stage('Test Docker Container') {
            steps {
               sh 'curl http://localhost:8090'
            }
        }

        stage('Clean Environment') {
            steps {
                sh 'docker stop shadow1'
                sh 'docker rm shadow1'
            }
        }
    }
    post {
        success {
            slackSend message:"A new version of project is succesful build - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }
    }
}