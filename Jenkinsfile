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
        // stage('Trigger config change pipeline'){

        //     steps{

        //         script{
        //             sh '''
        //             # Set Jenkins authentication credentials
        //             JENKINS_USER=hesblac
        //             JENKINS_TOKEN=111158f4ea1a118e8650f4b23f39a744d6

        //             # Set URL of Jenkins job to trigger
        //             JOB_URL=http://54.166.45.158:8080/job/gitops_argocd_CD/

        //             # Build curl command to trigger Jenkins job with parameters
        //             CURL_CMD="curl -v -u $JENKINS_USER:$JENKINS_TOKEN \
        //                 -X POST \
        //                 --data-urlencode json='{\"parameter\": [{\"name\":\"IMAGE_TAG\", \"value\":\"$IMAGE_TAG\"}]}' \
        //                 $JOB_URL/buildWithParameters"

        //             # Execute curl command
        //             eval $CURL_CMD
        //         ''' 
        //         }
        //     }
        // }

        stage('Trigger Another Job') {
            steps {
                script {
                def jobName = "gitops_argocd_CD"
                def jenkinsUrl = "http://54.166.45.158:8080"
                def token = "111158f4ea1a118e8650f4b23f39a744d6"
                def username = "hesblac"
                def currentBuildNumber = env.BUILD_NUMBER


                //  // Create the Jenkins API URL for triggering the new job
                // def apiUrl = "${jenkinsUrl}/job/${jobName}/buildWithParameters"
                
                // // Set the parameters to be passed to the new job
                // def params = [
                //     "SOURCE_BUILD_NUMBER=${currentBuildNumber}"
                // ]
                
                // // Build the curl command
                // def curlCmd = "curl -X POST -u ${username}:${token} --data-urlencode ${params.collect { "\"${it}\"" }.join('&')} ${apiUrl}"
                
                // // Execute the curl command
                // sh(curlCmd)

                // Create the Jenkins API URL for triggering the job
                def apiUrl = "${jenkinsUrl}/job/${jobName}/build"

                // Set the environmental variable value
                def parameterValue = env.IMAGE_TAG
                // Set the parameters to be passed to the triggered job
                def params = [
                    "IMAGE_TAGG=${parameterValue}"
                ]
                
                // Build the curl command
                def curlCmd = "curl -X POST -u ${username}:${token} --data-urlencode ${params.collect { "\"${it}\"" }.join('&')} ${apiUrl}"
                
                // Execute the curl command
                sh(curlCmd)
                }
            }
        }
    }
}

