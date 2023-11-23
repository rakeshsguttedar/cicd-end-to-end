pipeline {
 
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
 
    stages {
 
        stage('Checkout'){
           steps {
                git credentialsId: 'github',
                url: 'https://github.com/rakeshsguttedar/cicd-end-to-end',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t rockondock/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           environment {
               REGISTRY_CREDENTIALS = credentials('docker-cred')
           }
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push rockondock/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'github',
                url: 'https://github.com/rakeshsguttedar/cicd-demo-manifests-repo.git',
                branch: 'main'
            }
        }

        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/rakeshsguttedar/cicd-demo-manifests-repo.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
