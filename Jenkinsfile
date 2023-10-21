pipeline {
environment { // Declaration of environment variables
DOCKER_ID = "faycal2020" // replace this with your docker-id
DOCKER_IMAGE = "datascientestapi"
DOCKER_TAG = "v.${BUILD_ID}.0" // we will tag our images with the current build in order to increment the value by 1 with each new build
}
agent any // Jenkins will be able to select all available agents
stages {
        stage(' Docker Build'){ // docker build image stage
            steps {
                script {
                sh '''
                 docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
                sleep 6
                '''
                }
            }
        }
        stage(' Docker run'){ // run container from our builded image
                steps {
                    script {
                    sh '''
                    echo "Cleaning existing container if exist"
                    docker rm -f $(docker ps -q)
                    docker-compose up
                    '''
                    }
                }
            }

        stage('Test Acceptance'){ // we launch the curl command to validate that the container responds to the request
            steps {
                    script {
                    sh '''
                    curl -i -X 'POST' -H 'Content-Type: application/json' -d '{"id": 1, "name": "toto", "email": "toto@email.com","password": "passwordtoto"}' http://localhost:80
                    if curl -X 'GET' -H 'accept: application/json' http://localhost:80/users | grep -qF "toto"; then
                        echo "La chaîne 'toto' a été trouvée dans la réponse."
                    else
                        echo "La chaîne 'toto' n'a pas été trouvée dans la réponse."
                    fi
                    '''
                    }
            }

        }
        stage('Docker Push'){ //we pass the built image to our docker hub account
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
            }

            steps {

                script {
                sh '''
                docker login -u $DOCKER_ID -p $DOCKER_PASS
                docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                '''
                }
            }

        }

    //     stage('Deploiement en dev'){
    //             environment
    //             {
    //             KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
    //             DOCKER_PASS = credentials("DOCKER_HUB_PASS")
    //             }
    //                 steps {
    //                     script {
    //                     sh '''
                        
    //                     helm upgrade myapp-release-dev myapp1/ --values myapp1/values.yaml -f myapp1/values-dev.yaml -n dev
    //                     '''
    //                     }
    //                 }

    //             }

    //     stage('Deploiement en prod'){
    //             environment
    //             {
    //             KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
    //             }
    //                 steps {
    //                 // Create an Approval Button with a timeout of 15minutes.
    //                 // this require a manuel validation in order to deploy on production environment
    //                         timeout(time: 15, unit: "MINUTES") {
    //                             input message: 'Do you want to deploy in production ?', ok: 'Yes'
    //                         }

    //                     script {
    //                     sh '''
                               
    //                      helm upgrade myapp-release-prod myapp1/ --values myapp1/values.yaml -f myapp1/values-prod.yaml -n prod
    //                     '''
    //                     }
    //                 }

    //             }

    //     stage('Prune Docker data') {
    //             steps {
    //                 sh 'docker system prune -a --volumes -f'
    //             }

    //     }
    // }
    //     post { // send email when the job has failed
    //         always {
    //             script {
    //                 slackSend botUser: true, color: 'good', message: 'Successful completion of ${env.JOB_NAME}', teamDomain: 'DEVOPS TEAM', tokenCredentialId: 'slack-bot-token'
    //             }
    //         }
            
            
            ..
            /*
            failure {
                echo "This will run if the job failed"
                mail to: "youssef.kadi@gmail.com",
                    subject: "${env.JOB_NAME} - Build # ${env.BUILD_ID} has failed",
                    body: "For more info on the pipeline failure, check out the console output at ${env.BUILD_URL}"
            }
            */
            ..
        }
    }