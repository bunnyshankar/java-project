pipeline {
  agent none
  environment {
	MAJOR_VERSION=1
  }
  stages {

    stage('Unit Tests') {

      agent {
	label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
      }
    }

    stage('build') {

      agent {
        label 'apache'
      }

      steps {
        sh 'ant -f build.xml -v'
	junit 'reports/result.xml'
      }
      post {
        success {
           archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }

   stage('deploy') {

      agent {
        label 'apache'
      }

     steps {
	sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
	sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
     }
   }

   stage("Running on CentOS") {
    
     agent {
	label 'CentOS'
     }
    
     steps {
	sh "wget http://ec2-52-221-220-112.ap-southeast-1.compute.amazonaws.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
     }
   }

   stage("Test on Debian") {
  
     agent {
	docker 'openjdk:8u121-jre'
     }
  
     steps {
	sh "wget http://ec2-52-221-220-112.ap-southeast-1.compute.amazonaws.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
     }
   }
   
   stage('Promote to Green') {
     
     agent {
        label 'apache'
      }
     
     when {
	branch 'master'
     } 
     steps {
	sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
     }
   }

  stage('Promote Development branch to Master') {

     agent {
        label 'apache'
      }

     when {
        branch 'development'
     }
     steps {
	echo "Stashing local changes"
	sh 'git stash'
	echo "checking out development Branch"
	sh 'git checkout development'
	echo "checking out master"
	sh 'git checkout master'
	echo "Merging Development into master"
	sh 'git merge development'
	echo "pushing into origin master"
	sh 'git push origin master'
	echo "tagging the release"
	sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
	sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
	
     }
   }


 }
}
