pipeline {
    agent any 
    
    environment {
        // Use a variable to store the image tag (build number)
        IMAGE_TAG = ""
    }
    
    stages {
        
        stage('Checkout'){
            steps {
                git credentialsId: '889e6fb2-cc60-4288-9d61-198b6822497a', 
                    url: 'https://github.com/gmanne11/CICD-end-to-end.git',
                    branch: 'main'
            }
        }
        
        stage('Show Build Number') {
            steps {
                script {
                    // Dynamically extract the build number
                    IMAGE_TAG = sh(script: 'echo ${BUILD_NUMBER}', returnStdout: true).trim()
                    echo "The build number is: ${IMAGE_TAG}"
                }
            }
        }
        
        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Build Docker Image'
                    docker build -t vivekmanne/cicd-e2e:${IMAGE_TAG} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo 'Logging into Docker Hub'
                        docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                        echo 'Pushing to Repository'
                        docker push vivekmanne/cicd-e2e:${IMAGE_TAG}
                        '''
                    }
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: '889e6fb2-cc60-4288-9d61-198b6822497a', 
                    url: 'https://github.com/gmanne11/CICD-end-to-end.git',
                    branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([string(credentialsId: 'Github-PAT', variable: 'GITHUB_TOKEN')]) {
                        sh '''
                        cd deploy
                        cat deploy.yaml
                        sed -i "s|vivekmanne/cicd-e2e:.*|vivekmanne/cicd-e2e:${IMAGE_TAG}|" deploy.yaml
                        cat deploy.yaml
                        git config --global user.email "gopim4959@gmail.com"
                        git config --global user.name "gmanne11"
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://$GITHUB_TOKEN@github.com/gmanne11/CICD-end-to-end.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
