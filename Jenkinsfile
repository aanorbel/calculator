pipeline {
    environment {
        registry = 'docker-registry-default.apps.192.168.33.10.nip.io/development/myimage'
        registryCredential = 'openshift-pusher'
        dockerImage = ''
        latestDockerImage = ''
        def scannerHome = tool 'sonar'
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
// Create an Artifactory server instance, as described above in this article:
                    def server = Artifactory.server('artifactory')
// Create and set an Artifactory Gradle Build instance:
                    def rtGradle = Artifactory.newGradleBuild()
                    rtGradle.resolver server: server, repo: 'libs-release'
                    rtGradle.deployer server: server, repo: 'libs-release-local'
// In case the Artifactory Gradle Plugin is already applied in your Gradle script:
                    rtGradle.usesPlugin = true

// Set a Gradle Tool defined in Jenkins "Manage":
                    rtGradle.tool = gradle
// Run Gradle:
                    def buildInfo = rtGradle.run rootDir: "", buildFile: 'build.gradle', tasks: 'artifactoryPublish'
// Alternatively, you can pass an existing build-info instance to the run method:
// rtGradle.run rootDir: "projectDir/", buildFile: 'build.gradle', tasks: 'clean artifactoryPublish', buildInfo: buildInfo

// Publish the build-info to Artifactory:
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
