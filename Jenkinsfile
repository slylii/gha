pipeline {

    agent {
        node {
            label 'docker'
        }
    }

    parameters {
        string(name: 'OS_NAME', defaultValue: 'alpine', description: '')
        string(name: 'IMAGE_TAG', defaultValue: "", description: '')
    }

    environment {
        TAG = "${params.IMAGE_TAG != "" ? "${params.IMAGE_TAG}" : "${GIT_COMMIT}"}"
    }

    stages {

        stage("Begin") {
            steps {
                checkout scm
            }
        }

        stage('Builder image and test') {
            steps {
                script {

                    builderImage = docker.build("cv-builder:${TAG}", "-f Dockerfile.${params.OS_NAME} --target builder .")
                    builderImage.withRun {c ->
                        "json"
                    }
                }
            }
        }

        stage('Release image') {
            steps {
                script {
                    docker.build("cv:${TAG}", "-f Dockerfile.${params.OS_NAME} .")
                }
            }
        }


    }

}