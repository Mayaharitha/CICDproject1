node{
   stage('SCM Checkout'){
     git 'https://github.com/Mayaharitha/CICDproject1.git'
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
	    
   stage('Build Docker Image'){
       sh 'docker build -t mayaharitha1/myweb:0.0.3 .'
   }
   
   stage('Docker Image Push'){
      withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
      sh "docker login -u mayaharitha1 -p ${dockerPassword}"
      }
      sh 'docker push mayaharitha1/myweb:0.0.3'
   }
   
   stage('Nexus Image Push'){
       sh "docker login -u admin -p admin 52.66.250.5:8085"
       sh "docker tag mayaharitha1/myweb:0.0.3 52.66.250.5:8085/maya:2.1.1"
       sh 'docker push 52.66.250.5:8085/maya:2.1.1'
   }
   
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   }
   
   stage('Docker deployment'){
       sh 'docker run -d -p 8090:8080 --name tomcattest mayaharitha1/myweb:0.0.3' 
   }
}

