node{
   stage('SCM Checkout'){
     git 'https://github.com/lperumal87/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/app.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Imager'){
   sh 'docker build -t lperumal87/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u lperumal87 -p ${dockerPassword}"
    }
   sh 'docker push lperumal87/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 15.207.87.246:8083"
   sh "docker tag lperumal87/myweb:0.0.2 15.207.87.246:8083/perumal:1.0.0"
   sh 'docker push 15.207.87.246:8083/perumal:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment in tomcat'){
   sh 'docker run -d -p 8090:8080 --name tomcattest lperumal87/myweb:0.0.2' 
   }
}
}
