pipeline {
    agent {
        node {
            label 'AGENT-1'
        }
    }
    environment {
        packageVersion = ''
        nexusURL = '172.31.83.22:8081'
    }
    options {
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters {
    

        booleanParam(name: 'Deploy', defaultValue: false, description: 'Toggle this value')

        // choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        // password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    }
    stages {
        stage('VPC') {
            steps {
                 sh """
                 cd 01-vpc
                 terraform init -reconfigure
                 terraform apply -auto-configure
                 """
            }
        }
        stage('SG') {
            steps {
                 sh """
                 cd 02-sg
                 terraform init -reconfigure
                 terraform apply -auto-configure
                 """
            }
        }
        stage('VPN') {
            steps {
                 sh """
                 cd 03-vpn
                 terraform init -reconfigure
                 terraform apply -auto-configure
                 """
            }
        }
        stage('DB ALB') {
            parallel {
                stage('DB') {
                    steps {
                        sh """
                        cd 04-database
                        terraform init -reconfigure
                        terraform apply -auto-configure
                        """
                    }
                }
                stage('ALB') {
                    steps {
                        sh """
                        cd 05-app-alb
                        terraform init -reconfigure
                        terraform apply -auto-configure
                        """
                    }
                }
            }
        }
    }
    // post build
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        failure { 
            echo 'this runs when pipeline is failed, used generally to send some alerts'
        }
        success{
            echo 'I will say Hello when pipeline is success'
        }
    }
}