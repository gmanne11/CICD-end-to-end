pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
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
                    echo "The build number is: ${BUILD_NUMBER}"
                }
            }
        }
        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t vivekmanne/cicd-e2e:${BUILD_NUMBER} .
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
                        docker push vivekmanne/cicd-e2e:${BUILD_NUMBER}
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
                        sed -i "s/cicd-e2e:/cicd-e2e: $BUILD_NUMBER/g" deploy.yaml
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
