pipeline {

    agent any

    environment{

        DOCKERHUB_USERNAME = 'hesblac'
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CRED = "dockerhub-id"
    }

    stages{

        stage('cleanup workspace'){

            steps{

                script{

                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){

            steps{

                script{
                    git credentialsId: 'github',
                    url: 'https://github.com/hesblac/gitops_project_one.git',
                    branch: 'main'
                    // withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    // }
                }
            }
        }
        stage('Build Docker image'){

            steps{

                script{

                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push docker image'){

            steps{

                script{

                    docker.withRegistry("",REGISTRY_CRED){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push("latest")
                    }
                }
            }
        }
        // stage('Delete Docker images'){

        //     steps{

        //         script{

        //             sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
        //             sh "docker rmi ${IMAGE_NAME}:latest"
        //         }
        //     }
        // }
        stage('Trigger config change pipeline'){

            steps{

                script{
                    sh '''
                    # Set Jenkins authentication credentials
                    JENKINS_USER=hesblac
                    JENKINS_TOKEN=111158f4ea1a118e8650f4b23f39a744d6

                    # Set URL of Jenkins job to trigger
                    JOB_URL=http://54.166.45.158:8080/job/gitops_argocd_CD/

                    # Build curl command to trigger Jenkins job with parameters
                    CURL_CMD="curl -v -u $JENKINS_USER:$JENKINS_TOKEN \
                        -X POST \
                        --data-urlencode json='{\"parameter\": [{\"name\":\"IMAGE_TAG\", \"value\":\"$IMAGE_TAG\"}]}' \
                        $JOB_URL/buildWithParameters"

                    # Execute curl command
                    eval $CURL_CMD
                ''' 
                }
            }
        }

    }
}