pipeline {
    agent any
    tools {
        maven 'maven3'
    }

    stages{
        stage('Build'){
            steps{
                 sh script: 'mvn clean package'
                 archiveArtifacts artifacts: 'target/*.war', onlyIfSuccessful: true
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
                script {
                    def testResults = findFiles(glob: 'build/reports/**/*.xml')
                    for(xml in testResults) {
                        touch xml.getPath()
            }
        
        post {
            always {
                 junit 'build/reports/**/*.xml'
             }
        }
        }
        stage('Upload War To Nexus'){
            steps{
                script{

                    nexusArtifactUploader artifacts: [
					    [
						    artifactId: 'simple-app', 
							classifier: '', 
                            file: 'target/simple-app-1.0.0.war', 
							type: 'war'
							]
						], credentialsId: 'e0850b2e-0788-4e40-bf42-53edd03c69b1', 
						   groupId: 'home.java', 
						   nexusUrl: 'localhost:8081', 
						   nexusVersion: 'nexus3', 
						   protocol: 'http', 
						   repository: 'maven-nexus-repo', 
						   version: '${BUILD_NUMBER}'
                    }
            }
        }
        stage('Upload War To Tomcat'){
            steps{
                script{

                    sh "wget http://192.168.1.172:8081/repository/maven-nexus-repo/home/java/simple-app/1.0.0/simple-app-1.0.0.war"
                    sh "mv simple-app-1.0.0.war devops.war"
                    deploy adapters: [tomcat9(credentialsId: '690674a3-b5cc-489a-a643-38218051e29e', 
                    path: '', url: 'http://ec2-3-21-41-189.us-east-2.compute.amazonaws.com:8080/')], 
                    contextPath: 'devops', 
                    war: '**/*.war'
                    }
            }
        }
    }
}