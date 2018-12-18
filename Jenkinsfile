pipeline {
    environment {
        registry = "organisation/calculator"
        registryCredential = 'openshift-pusher'
    }
    agent any
    stages {
        stage("Compile") {
            steps {
                sh "./gradlew clean compileJava"
            }
        }

//        stage("Code coverage") {
//            steps {
//                sh "./gradlew jacocoTestReport"
//                sh "./gradlew jacocoTestCoverageVerification"
//            } z
//        }

        stage("Package") {
            steps {
                sh "./gradlew build"
            }
        }

        stage("Docker build") {
            steps {
                docker.build("organisation/calculator")
                sh "docker build -t organisation/calculator:${env.BUILD_ID} ."
            }
        }

        stage("Docker push") {
            steps {
                sh "echo 'docker login and docker push'"
                //  sh "docker login -u nikhilnidhi -p chinki12"

                //  sh "docker push nikhilnidhi/calculator_1"
                script {
                    docker.withRegistry('', registryCredential) {
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
