pipeline {
    agent any

    //only gradle, maven and jdk are supported
    //remove 'gradle "Gradle-7.1.1'" from here if using 'withGradle(){}' wrapper within steps
    tools {
        maven 'Maven-3.8.1'
        gradle 'Gradle-7.1.1'
    }

    environment {
        NEW_VERSION = '1.3.0'
        SERVER_CREDENTIALS = credentials('server-credentials') //declare here if needed in multiple stages
    }

    parameters {
        string(name: 'VERSION', defaultValue: '', description: '')
        choice(name: 'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: '')
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }


    triggers {
        pollSCM '* * * * *'
    }

    stages {
        stage('build frontend') {
            steps {
                echo 'executing yarn...'
                nodejs('Node-10.17'){
                     sh 'yarn install'
                    //  sh "mvn install" //when maven is "maven 'Maven-3.8.1'" is not declared in tools
                }
            }
        }

        stage('build backend') {
            steps {
                script {
                    echo 'building the application...'
                    echo "building version ${NEW_VERSION}"

                    //method1
                    echo "using server credentials ..."
                    sh "some script ${SERVER_CREDENTIALS}"

                    //method2
                    withCredentials([
                        usernamePassword(credentials: 'server-credentials', usernameVariable: USER, passwordVariable: PWD)
                    ]){
                        sh "some script ${USER} ${PWD}"
                    }
                }
            }
        }

        stage('test') {
            when {
                expression {
                    params.executeTests == true
                }
            }
            steps {
                echo 'executing gradle...'

                //method1
                sh './gradlew -v'

                //method2
                // withGradle(){
                //     sh './gradlew -v'
                // }

                //using maven
                echo 'executing mvn...'
                // sh "mvn install" //POM file must be present
            }
        }

        stage("deploy") {
            steps {
                echo 'deploying the application ...'
                echo "deploying version ${params.VERSION}"
            }
        }

    }
}