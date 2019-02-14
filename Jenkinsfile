pipeline {
    agent {
        label 'mesos'
    }

    environment {
        IMAGE = "dcos-ansible-bundle"

        // Get credentials for publishing to Docker hub.
        DOCKER = credentials('docker-hub-credentials')
    }

    stages {
        stage('docker login') {
            steps {
                // Login to the Docker registry.
                sh("docker login -u ${DOCKER_USR} -p ${DOCKER_PSW}")
            }
        }

        stage('build') {
            steps {
                // Build Docker image.
                sh("docker build -t mesosphere/${IMAGE}:latest .")
            }
        }

        stage('publish') {
            steps {
                script {
                    // Calculate Docker image tag based on commit number and id.
                    env.dockerTag = sh(script: "echo \$(git rev-parse --abbrev-ref HEAD)-\$(git rev-parse --short HEAD)", returnStdout: true).trim()

                    // Tag and push the image we built earlier.
                    sh("docker tag mesosphere/${IMAGE}:latest mesosphere/${IMAGE}:${env.dockerTag}")
                    sh("docker push mesosphere/${IMAGE}:${env.dockerTag}")
                    sh("docker push mesosphere/${IMAGE}:latest")
                }
            }
        }
    }
}
