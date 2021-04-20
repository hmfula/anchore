pipeline {
  environment {
    registry = "hmfula/anchore"
    registryCredential = 'docker'
    dockerImage =  ''
}
agent any
stages {
  stage('Cloning Git') {
    steps {
      git 'https://github.com/hmfula/anchore.git'
 }
}
stage('Building image') {
  steps{
    script {
      dockerImage = docker.build registry + ":$BUILD_NUMBER"
      }
    }
  }
 
  stage('Deploy Image') {
  steps{
    script {
      docker.withRegistry( '', registryCredential ) {
      dockerImage.push()
      }
   }
 }
}
  
stage('Parallel') {
      Analyze: {
        writeFile file: anchorefile, text: 'docker.io/hmfula/anchore:' + $BUILD_NUMBER + " " + dockerfile
        anchore annotations: [[key: 'from', value: 'Jenkins']], autoSubscribeTagUpdates: false, bailOnFail: false, engineRetries: '10000', name: 'anchore_images'
      }
  }
  
 
stage('Cleanup') {
  steps {
    sh'''
      for i in `cat anchore_images | awk '{print $1}'`;do docker rmi $i; done
 '''
      }
    }
  }
}
