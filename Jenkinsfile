pipeline {
    agent { label "vinod" }

    stages {

        stage("Code") {
            steps {
                echo "Code checkout completed by Jenkins"
            }
        }

        stage("Build") {
            steps {
                echo "This is building the code"
                sh "docker build -t app-notes:latest ."
            }
        }

        stage("Test") {
            steps {
                echo "This is testing the code"
            }
        }

        stage("Deploy") {
            steps {
                echo "This is deploying the code"

                withCredentials([string(credentialsId: 'django_env', variable: 'DJANGO_ENV')]) {
                    sh '''
                        printf "%s\\n" "$DJANGO_ENV" > .env
                        docker compose down
                        docker compose up -d
                        rm -f .env
                    '''
                }
            }
        }
    }
}
