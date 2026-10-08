pipeline {
    agent { label "vinod" }

    stages {

        stage("Code") {
            steps {
                echo "This is cloning the code"
                git url: "https://github.com/Sharath-1863/django-notes-app.git", branch: "main"
                echo "code cloning successful"
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

                withCredentials([string(credentialsId: 'django_env', variable: 'ENV_FILE')]) {
                    sh '''
                        printf "%s\\n" "$ENV_FILE" > .env
                        docker compose up -d
                        rm -f .env
                    '''
                }
            }
        }
    }
}
