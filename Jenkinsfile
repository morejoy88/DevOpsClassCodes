
pipeline{
    tools{
        jdk 'myjava'
        maven 'mymaven'
    }
    agent none
      stages{
           stage('Checkout'){
               agent any
               steps{
		 echo 'cloning..'
                 git 'https://github.com/morejoy88/DevOpsClassCodes.git'
              }
          }
          stage('Compile'){
              agent any
              steps{
                  echo 'compiling..'
                  sh 'mvn compile'
              }
          }
          stage('CodeReview'){
              agent any
              steps{
                  sh 'mvn pmd:pmd'
              }
          }
           stage('UnitTest'){
               agent any
              steps{
                  sh 'mvn test'
              }
               post {
               success {
                   junit 'target/surefire-reports/*.xml'
               }
           }	
          }

          stage('Package'){
              agent any
              steps{
                  sh 'mvn package'
              }
          }   
		
	stage('Deploy'){
	      agent any
	      steps{
			sh label: '', script:'''rm -rf mydockerfile
			mkdir mydockerfile
			cd mydockerfile
			cp /var/lib/jenkins/workspace/pipeline1/target/addressbook.war .
			touch dockerfile
			cat <<EOT>> dockerfile
			FROM tomcat
			ADD addressbook.war /usr/local/tomcat/webapps
			EXPOSE 8080
			CMD ["catalina.sh", "run"]'''
		      	sh 'sudo docker rm -f myimage:$previousSuccessfulBuild'
		      	sh 'sudo docker rmi myimage:$previousSuccessfulBuild'
			sh 'sudo docker build -t myimage:$BUILD_NUMBER -f /var/lib/jenkins/workspace/pipeline1/mydockerfile/dockerfile .'
			sh 'sudo docker run -itd -p 7000:8080 myimage:$BUILD_NUMBER'
	      }
	    }

          
      }
}
