node{
     
    stage('SCM Checkout'){
        git url: 'https://github.com/MilanBSoni/Jenkins_Java.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    
    stage('Build Docker Image'){
        sh 'docker build -t dhareshmilan/java-web-app .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
          sh "docker login -u dhareshmilan -p ${Docker_Hub_Pwd}"
        }
        sh 'docker push dhareshmilan/java-web-app'
     }
     
      stage('Run Docker Image In Dev Server'){
        
        def dockerRun = ' docker run  -d -p 8080:8080 --name java-web-app dhareshmilan/java-web-app'
         
         sshagent(['DOCKER_SERVER']) {
          sh 'ssh -o StrictHostKeyChecking=no ec2-user@ip-172-31-22-212 docker stop java-web-app || true'
          sh 'ssh  ec2-user@ip-172-31-22-212 docker rm java-web-app || true'
          sh 'ssh ec2-user@ip-172-31-22-212 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ec2-user@ip-172-31-22-212 ${dockerRun}"
       }
       
    }
     
     
}
