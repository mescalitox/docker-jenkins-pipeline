node {
  checkout scm
  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"
  stage('Package') {
    dir('webapp') {
		bat 'dir'
      bat 'mvn clean package -DskipTests'
	   echo "stage1"
    }
  }

  stage('Create Docker Image') {
    dir('webapp') {
		echo "DI 1"
      docker.build("arungupta/docker-jenkins-pipeline:${env.BUILD_NUMBER}")
	  echo "DI 2"
    }
  }

  stage ('Run Application') {
    try {
      // Start database container here
      // bat 'docker run -d --name db -p 8091-8093:8091-8093 -p 11210:11210 arungupta/oreilly-couchbase:latest'

      // Run application using Docker image
	  echo "stage RA1"
      bat "SET DB=`docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' db`"
	  echo "stage RA2"
      bat "docker run -e DB_URI=$DB arungupta/docker-jenkins-pipeline:${env.BUILD_NUMBER}"

      // Run tests using Maven
      //dir ('webapp') {
      //  bat 'mvn exec:java -DskipTests'
      //}
    } catch (error) {
    } finally {
      // Stop and remove database container here
      //bat 'docker-compose stop db'
      //bat 'docker-compose rm db'
    }
  }

  stage('Run Tests') {
    try {
      dir('webapp') {
        bat "mvn test"
        docker.build("arungupta/docker-jenkins-pipeline:${env.BUILD_NUMBER}").pubat()
      }
    } catch (error) {

    } finally {
      junit '**/target/surefire-reports/*.xml'
    }
  }
}
