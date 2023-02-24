node{
   stage('SCM Checkout'){
     git 'https://github.com/crsathish3103/my-app.git'
   }
   stage('maven-buildstage'){

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
   sh 'docker build -t crsathish3103/mysathi:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerpassword')]) {
   sh "docker login -u crsathish3103 -p ${dockerpassword}"
    }
   sh 'docker push crsathish3103/mysathi:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.235.13.199:8083"
   sh "docker tag crsathish3103/mysathi:0.0.2 13.235.13.199:8083/sathi:1.0.0"
   sh 'docker push 13.235.13.199:8083/sathi:1.0.0'
   } 
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
    }
    stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest crsathish3103/mysathi:0.0.2' 
   }
}
