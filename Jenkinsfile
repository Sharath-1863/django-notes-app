// pipeline {
//     agent { label "vinod" }

//     stages {

//         stage("Code") {
//             steps {
//                 echo "Code checkout completed by Jenkins"
//             }
//         }

//         stage("Build") {
//             steps {
//                 echo "This is building the code"
//                 sh "docker build -t app-notes:latest ."
//             }
//         }

//         stage("Test") {
//             steps {
//                 echo "This is testing the code"
//             }
//         }

//         stage("Deploy") {
//             steps {
//                 echo "This is deploying the code"

//                 withCredentials([string(credentialsId: 'django_env', variable: 'DJANGO_ENV')]) {
//                     sh '''
//                         printf "%s\\n" "$DJANGO_ENV" > .env
//                         docker compose down
//                         docker compose up -d
//                         rm -f .env
//                     '''
//                 }
//             }
//         }
//     }
// }


pipeline {
    agent { label "vinod" }

    environment {
        DOCKERHUB_REPO = "sharath2003/django-notes-app"
    }

    stages {

        stage("Code") {
            steps {
                echo "Code checkout completed by Jenkins"
            }
        }

        stage("Build") {
            steps {
                echo "Building Docker image"

                sh '''
                    docker build -t $DOCKERHUB_REPO:$BUILD_NUMBER .
                    docker tag $DOCKERHUB_REPO:$BUILD_NUMBER $DOCKERHUB_REPO:latest
                '''
            }
        }

        stage("Push to Docker Hub") {
            steps {
                echo "Pushing Docker images to Docker Hub"

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub_creds',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_TOKEN'
                    )
                ]) {
                    sh '''
                        set +x

                        echo "$DOCKERHUB_TOKEN" | docker login \
                            --username "$DOCKERHUB_USER" \
                            --password-stdin

                        docker push $DOCKERHUB_REPO:$BUILD_NUMBER
                        docker push $DOCKERHUB_REPO:latest

                        docker logout
                    '''
                }
            }
        }

        stage("Test") {
            steps {
                echo "Docker image published successfully"
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploying the published Docker image"

                withCredentials([
                    string(
                        credentialsId: 'django_env',
                        variable: 'DJANGO_ENV'
                    )
                ]) {
                    sh '''
                        set -eu

                        printf "%s\\n" "$DJANGO_ENV" > .env
                        chmod 600 .env

                        export IMAGE_TAG="$BUILD_NUMBER"

                        docker compose pull django_app
                        docker compose up -d --no-build

                        rm -f .env
                    '''
                }
            }
        }
    }
}
