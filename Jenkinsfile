pipeline{
    agent any

    environment {
        TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
        max = 20
        random_num = "${Math.abs(new Random().nextInt(max+1))}"
//         docker_password = credentials('dockerhub_password')
    }

    stages{
        stage("Workspace Cleanup") {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout Git') {
            steps {
                git branch: 'main', credentialsId: 'c0cb85d4-4b8a-40de-b551-c1c1c1b5f183', url: 'https://github.com/Lukobet/php-todo-proj20.git'
            }
        }

        stage('Building application ') {
            steps {
                script {
                    
                    sh " docker login -u lukobet -p ${env.PASSWORD}"
                    sh " docker build -t lukobet/todo:${env.TAG} ."
                }
            }
        }

        stage('Creating docker container') {
            steps {
                script {
                    sh " docker run -d --name todo-app-${env.random_num} -p 8000:8000 lukobet/todo:${env.TAG}"
                }
            }
        }

        stage("Smoke Test") {
            steps {
                script {
                    sh "sleep 60"
                    sh "curl -I 18.209.160.169:8000"
                }
            }
        }

        stage("Publish to Registry") {
            steps {
                script {
                    sh " docker push lukobet/todo:${env.TAG}"
                }
            }
        }

        stage ('Clean Up') {
            steps {
                script {
                    sh " docker stop todo-app-${env.random_num}"
                    sh " docker rm todo-app-${env.random_num}"
                    sh " docker rmi lukobet/todo:${env.TAG}"
                }
            }
        }

        stage ('logout Docker') {
            steps {
                script {
                    sh " docker logout"
                }
            }
        }
    }
   
}
