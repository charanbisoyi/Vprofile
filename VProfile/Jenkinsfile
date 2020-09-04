node() {
    // some block
   stage('SCM Checkout'){
     git 'https://github.com/ashok-g/VProfile.git'
   }
    
   stage('Compile-Package'){
      // Get maven home path
      def mvnHome =  tool name: 'maven-3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
   }
   stage('SonarQube Analysis') {
      def mvnHome =  tool name: 'maven-3', type: 'maven'
      withSonarQubeEnv('sonar-6') { 
         sh "${mvnHome}/bin/mvn sonar:sonar"
         }
     }
    
    stage("Quality Gate Statuc Check"){
          timeout(time: 1, unit: 'HOURS') {
              def qg = waitForQualityGate()
              if (qg.status != 'OK') {
                   slackSend baseUrl: 'https://hooks.slack.com/services/',
                   channel: '#jenkins-pipeline-demo',
                   color: 'danger', 
                   message: 'SonarQube Analysis Failed', 
                   teamDomain: 'javahomecloud',
                   tokenCredentialId: 'slack-demo'
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
              }
              
          }
    }
              
    stage('nexus-push-artifact'){
    nexusArtifactUploader artifacts: [[artifactId: 'myapp', classifier: '',
     file: 'target/myapp-v1.war', type: 'war']], credentialsId: 'f3390c6a-2656-4419-a68d-ddc7ebd8a426',
     groupId: 'test1', nexusUrl: 'ip', nexusVersion: 'nexus3', protocol: 'https', 
     repository: 'test_repo', version: 'v1'
    }
              
    
    stage('docker-images-nexus3')  {
        //
            
        
        sh '''sudo systemctl stop docker '''
        sh '''     /etc/docker/daemon.json << {
                "insecure-registries": [
            "ashok3131:8082",
             "ashok3131:8083"
              ],
              "disable-legacy-registry": true
            }'''


    sh " sudo systemctl restart docker"

  sh "docker login -u ashok3131 -p "ashok$3131" ashok3131:8082"
  sh "docker login -u ashok3131 -p ashok$3131 ashok3131:8083"

      sh " docker tag jenkins:latest ashok3131:8083/jenkins:latest"
     sh "docker push ashok3131:8083/jenkins:latest"

        
    }
        
        
        
 

     }       
              
              
     stage('Email Notification'){
      mail bcc: '', body: '''Hi Welcome to jenkins email alerts
      Thanks
      Hari''', cc: '', from: '', replyTo: '', subject: 'Jenkins Job', to: 'hari.kammana@gmail.com'
   }
              
              
              
}
