pipeline {
    environment {
        registry = 'docker-registry-default.apps.192.168.33.10.nip.io/development/myimage'
        registryCredential = 'openshift-pusher'
        dockerImage = ''
        latestDockerImage = ''
        def scannerHome = tool 'sonar'
        def uploadSpec = """{
           "files": [
                       {
                       "pattern": "build/libs/*.jar",
                       }     
                     ] 
               }"""
    }
    agent any
    stages {
        stage('SonarQube analysis') {
            steps {
                script {
                    withSonarQubeEnv('sonar') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        stage("Compile") {
            steps {
                sh "./gradlew clean compileJava"
            }
        }

        stage("Package") {
            steps {
                sh "./gradlew build"
            }
        }

        stage("Publish to Artifacatory") {
            steps {
                script {

                    def server = Artifactory.server('artifactory')
                    server.bypassProxy = true
                    def buildInfo = server.upload spec: uploadSpec

                    server.publishBuildInfo buildInfo
                }
            }
        }

        stage("Docker build") {
            steps {
                script {
                    latestDockerImage = docker.build(registry)
                    dockerImage = docker.build("${registry}:${env.BUILD_ID}")
                }
            }
        }

        stage("Docker push") {
            steps {

                script {
                    docker.withRegistry('https://docker-registry-default.apps.192.168.33.10.nip.io', registryCredential) {
                        dockerImage.push()
                        latestDockerImage.push()
                    }
                }
            }
        }
        stage("Deploy to staging") {
            steps {
                sh "echo 'Deploy to staging'"

            }
        }

        stage("Acceptance test") {
            steps {
                sh "echo 'Acceptance test'"

            }
        }
    }
    post {
        always {
            sh "echo 'Acceptance test'"
        }
    }
}
