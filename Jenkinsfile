pipeline {
    environment {
        registry = 'docker-registry-default.apps.192.168.33.10.nip.io/development/myimage'
        registryCredential = 'openshift-pusher'
        dockerImage = ''
    }
    agent any
    stages {
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

        stage("Docker build") {
            steps {
                script {
//                    docker.build(registry)
                    dockerImage = docker.build("${registry}:${env.BUILD_ID}")
                }
            }
        }

        stage("Docker push") {
            steps {
                sh "echo 'docker login and docker push'"
                //  sh "docker login -u nikhilnidhi -p chinki12"

                //  sh "docker push nikhilnidhi/calculator_1"
                script {
                    docker.withRegistry('docker-registry-default.apps.192.168.33.10.nip.io', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage("Deploy to staging") {
            steps {
                sh "echo 'Deploy to staging'"

                // sh "docker run -d --rm -p 8765:8080 --name calculator_1 nikhilnidhi/calculator_1"
                //	 sh "docker-compose up -d"
            }
        }

        stage("Acceptance test") {
            steps {
                sh "echo 'Acceptance test'"

                //sleep 60
                //sh "./acceptance_test_docker.sh"
            }
        }
    }
    post {
        always {
            sh "echo 'Acceptance test'"

            // sh "docker-compose down"
        }
    }
}
