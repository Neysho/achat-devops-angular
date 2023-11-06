pipeline {
    agent any
    
    environment{
        DOCKERHUB_CREDENTIALS=credentials('docker-hub-neysho')
    }
       stages{
             stage('checkout'){
                        steps{
                        //  deleteDir()
                         checkout scmGit(branches: [[name: '*/master']], extensions: [],
                          userRemoteConfigs: [[credentialsId: 'github-neysho',
                           url: 'https://github.com/Neysho/achat-devops-angular.git']])
                       }
                  }
           
             stage('NPM install'){
                tools { nodejs "nodejs-14.21.3" }
                steps{
                    sh 'npm install'
                }
            }
            stage('build'){
                tools { nodejs "nodejs-14.21.3" }
                steps{
                    sh 'npm run build --prod'
                }
            }
            stage('docker build'){
                steps{
                    container('docker') {
                        sh ''' ls
                               docker build -t neysho/achat-frontend:1 .
                               echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                               docker push neysho/achat-frontend:1
                        '''
               }
              }
            }

    }
     post {
        // Clean after build
        always {
            cleanWs()
            }
        success {
                    slackSend color: "good",  message: 'Pipeline completed successfully!',
                     tokenCredentialId: 'slack-alert-bot'
              }  
        failure {
                    slackSend color: "danger",
                     message: 'Frontend Pipeline failed. Check logs.',
                     tokenCredentialId: 'slack-alert-bot'
             }    
          }
}