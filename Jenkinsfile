node('Slave_CICD'){
    stage('SCM Checkout'){
     git 'https://github.com/magesh87/CICD1.git'
   }
   
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
    stage('Build Docker Imager'){
   sh 'docker build -t magesh87/myweb:0.0.2 .'
   }
   
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'magesh87', variable: 'dockerPassword')]) {
   sh "docker login -u magesh87 -p ${dockerPassword}"
    }
   sh 'docker push magesh87/myweb:0.0.2'
   }
   
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   
    }
   
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest magesh87/myweb:0.0.2' 
   }
   
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 18.216.143.136:8083"
   sh "docker tag magesh87/myweb:0.0.2 18.216.143.136:8083/magesh87:nexus"
   sh 'docker push 18.216.143.136:8083/magesh87:nexus'
   }
   
}
