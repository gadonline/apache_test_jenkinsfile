#!/usr/bin/env groovy


properties([disableConcurrentBuilds()])

pipeline {
    agent {
        label 'master'
    }
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
        timestamps()
    }
    
    parameters {
        string(name: 'HTML_TEXT', defaultValue: 'test', description: '')
    }
    
    stages {
        stage("Настройка окружения") {
            agent {
                label 'master'
            }
            steps {
                script {
                    echo "Настройка окружения"
                    
                    dir('apache_test_playbook') {
                        git credentialsId: 'deploy', url: 'ssh://git@127.0.0.1:3001/git/apache_test_playbook.git'
                    }
                }
            }
        }
        stage("Деплой") {
            agent {
                label 'master'
            }
            steps {
                script {
                    ansiblePlaybook(
                        inventory: 'apache_test_playbook/inventory.ini',
                        playbook: 'apache_test_playbook/apache-setup.yml',
                        credentialsId: 'deploy',
                        disableHostKeyChecking: true,
                        extraVars: [
                            text: params.HTML_TEXT,
                        ])
                }
            }
        }
    }
    post {
        always {
            script {
                if (currentBuild.result == 'SUCCESS') {
                    echo 'SUCCESS'
                } else if (currentBuild.result == 'FAILURE') {
                    echo 'FAILURE'
                } else if (currentBuild.result == 'ABORTED') {
                    echo 'ABORTED'
                }
            }
        }
    }
}
