pipeline {
    agent any
    stages {
        
        //1. Build the app
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
            
        //2. Build the Dockerfile
        stage('Docker Build') {
            when {
                    branch 'master'
            }
            
            steps {
            // adding script-style due to the limitation in the Jenkins docker plugin
                script {
                  app = docerk.build("mostafaradwan/train-schedule")
                    app.inside {
                        // A quick smoke test of the web app inside a container
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
            
            //3 Push the docker image to the docker hub
            stage ('Push Docker Image') {
                when {
                    branch 'master'
                }
                
                steps {
                    
                    script {
                        script {
                            docker.withRegistry('https://registry.hub.docker.com','docker_hub_login') {
                                app.push("${env.BUILD_NUMBER}")
                                app.push("latest")
                            }
                        }
                    }
                }
            }
            
            }
        }
    }
}
