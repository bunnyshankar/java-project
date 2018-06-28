pipeline {
  agent none
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
	sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
     }
   }

   stage("Running on CentOS") {
    
     agent {
	label 'CentOS'
     }
    
     steps {
	sh "wget http://ec2-52-221-220-112.ap-southeast-1.compute.amazonaws.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
     }
   }

  }
}
