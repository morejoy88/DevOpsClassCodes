
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
           stage('MetricCheck'){
               agent {label 'node1'}
              steps{
		     
		     catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
			     git 'https://github.com/morejoy88/DevOpsClassCodes.git'
			     sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
                      }   
              }
		   
               post {
               success {
	           cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false                  
               }
           }		
          }
          stage('Package'){
              agent any
              steps{
                  sh 'mvn package'
              }
          }
	  
	  stage('deploy'){
              agent any
              steps{
		      sh label: 'My Pipeline Test', script: rm -rf jenkindockerfile
		      mkdir jenkindockerfile
		      cd jenkindockerfile
		      cp /var/lib/jenkins/workspace/pipeline1/target/addressbook.war .
		      //touch dockerfile
		      cat <<EOT touch dockerfile
			FROM tomcat
			ADD addressbook.war /usr/local/tomcat/webapps
			EXPOSE 8888
			CMD ["catalina.sh", "run"]
			EOT
		      sudo docker build -t myaddrbook:$BUILD_NUMBER .
		      sudo docker run -d -P myaddrbook:$BUILD_NUMBER
              }
          }
          
      }
}
