pipeline {
    agent any
    
    tools{
        nodejs 'node20'
    }

    stages {
        stage('Git-checkout Repo-I') {
            steps {
                echo 'Git checkout'
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/shubham9511s/Node-app.git'
            }
        }
        
         stage("Docker Image Build,tag and push") {
              environment {
                            DOCKER_IMAGE = "shubhamshinde2206/node-app:${BUILD_NUMBER}"
             }
            steps {
                script {
                  withDockerRegistry(credentialsId: 'docker-token', toolName: 'docker'){
                    sh 'docker build -t ${DOCKER_IMAGE} node-project/.'
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    dockerImage.push()
                      
                  }
                    
                }
            }
        }
        
        stage('Checkout Code from Manifest Repo-II') {
            steps {
                    git branch: 'main', changelog: false, poll: false, url: 'https://github.com/shubham9511s/manifest-file.git'
            }
        }
        stage('Update Deployment file') {
            environment {
                GIT_REPO_NAME = "manifest-file"
                GIT_USER_NAME = "shubham9511s"
                REPO_IMAGE_NAME = "shubhamshinde2025/node-app"
            }
            steps {
                   withCredentials([string(credentialsId: 'github-token', variable: 'TOKEN')]) {
                        sh '''
                            git config user.email "shubham.ssc100@gmail.com"
                            git config user.name "shubham9511s"
                            BUILD_NUMBER=${BUILD_NUMBER}
                            echo $BUILD_NUMBER
                            imageTag=$(grep -oP '(?<=node-app:)[^ ]+' manifest/main.yml)
                            echo $imageTag
                            sed -i "s|${REPO_IMAGE_NAME}:${imageTag}|${REPO_IMAGE_NAME}:${BUILD_NUMBER}|" manifest/main.yml
                            git add manifest/main.yml
                            git commit -m "Update deployment Image to version \${BUILD_NUMBER}"
                            git push https://${TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                        '''
                    }
                }
            }
        
        
        
    }
}
