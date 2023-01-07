pipeline {  
  agent any
   
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))  
    } 
  
  environment {
    TAG = sh(returnStdout: true, script: 'echo $(git rev-parse --short HEAD)').trim()
  }
  stages {
    stage('Build Image') {
        steps {
            script {
                if (env.BRANCH_NAME == 'dev') {  
            sh 'docker build -t triagungtio/cilist-fe:0.$BUILD_NUMBER-dev .'                                  
                }
                 else if (env.BRANCH_NAME == 'staging') {
            sh 'docker build -t triagungtio/cilist-fe:0.$BUILD_NUMBER-staging .'   
                }
                else if (env.BRANCH_NAME == 'main') {
            sh 'docker build -t triagungtio/cilist-fe:0.$BUILD_NUMBER-production .' 
                }
                else {
                    sh 'echo Nothing to Build'
                }
            }
        }
    }
    stage('Push to Registry') {
        steps {
            script {
             if (env.BRANCH_NAME == 'dev') {
            sh 'docker push triagungtio/cilist-fe:0.$BUILD_NUMBER-dev'
                }
                else if (env.BRANCH_NAME == 'staging') {
            sh 'docker push triagungtio/cilist-fe:0.$BUILD_NUMBER-staging'
                }
                else if (env.BRANCH_NAME == 'main') {
            sh 'docker push triagungtio/cilist-fe:0.$BUILD_NUMBER-production'
                }
                else {
                    sh 'echo Nothing to Push'
                }
        }
      }
    }
    // stage('Deploy to Kubernetes Cluster') {
    //     steps {
    //     script {
    //          if (env.BRANCH_NAME == 'dev') {
    //                 withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: '') {                       
    //                     sh 'cat deployment/dev/fe_app.yaml | sed "s/{{NEW_TAG}}/0.$BUILD_NUMBER-dev/g" |  kubectl apply -f -'   
    //              }
    //             }
    //             else if (env.BRANCH_NAME == 'staging') {
    //                 withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: '') {
    //                     sh 'cat deployment/staging/fe_app.yaml | sed "s/{{NEW_TAG}}/0.$BUILD_NUMBER-staging/g" |  kubectl apply -f -'   
    //              }
    //             }
    //             else if (env.BRANCH_NAME == 'main') {
    //                 withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: '') {
    //                     sh 'cat deployment/production/fe_app.yaml | sed "s/{{NEW_TAG}}/0.$BUILD_NUMBER-production/g" |  kubectl apply -f -'   
    //              }
    //             }
    //             else {
    //                 sh 'echo Nothing to deploy'
    //             }
    //     }
    //   }
    // } 
}

 post {
        success {
               script {
               if (env.BRANCH_NAME == 'dev' || env.BRANCH_NAME == 'staging' || env.BRANCH_NAME == 'prod' ) {
                slackSend channel: '#jenkins',
                    color: 'good',
                    message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}" 
               } 
               }
        } 
        failure {
                 slackSend channel: '#jenkins',
                        color: 'danger',
                        message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
         }
    }

}





