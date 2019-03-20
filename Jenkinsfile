pipeline {
    environment {
        registry = "ronaldb/blog-test"
        registryCredential = 'dockerhub'
        dockerImage = ''
    }
    agent none
    stages {
        stage('Cloning Git') {
            agent any
            steps {
                git 'https://github.com/ronaldb/jekyll-test.git'
            }
        }
        stage('Build website') {
            agent {
                docker
                {
                    image 'jekyll/jekyll:3.8'
                    args '''
                        -u root:root
                        -v "${WORKSPACE}:/srv/jekyll"
                    '''
                }
            }
            steps {
                sh '''
                    jekyll build
                '''
            }
        }
        stage('Build and deploy docker image') {
            agent any
            stages {

                stage('Building image') {
                    steps{
                        script {
                            dockerImage = docker.build registry + ":$BUILD_NUMBER"
                        }
                    }
                }
                stage('Deploy Image') {
                    steps{
                        script {
                            docker.withRegistry( '', registryCredential ) {
                                dockerImage.push()
                            }
                        }
                    }
                }
                stage('Remove Unused docker image') {
                    steps{
                        sh "docker rmi $registry:$BUILD_NUMBER"
                    }
                }
            }
        }
    }
}
