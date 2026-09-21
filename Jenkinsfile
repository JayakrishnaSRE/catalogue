pipeline {
    agent {
        label 'AGEN-1'
    }
    environment {
        COUSRSE = "myapp"
        appVersion = "1.0.0"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
    }
    options {
        timeout(time: 10, unit: 'HOURS')
        disableConcurrentBuilds()
    }

    //     parameters {
    //     string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
    //     text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
    //     booleanParam(name: 'DEPLOY', defaultValue: false, description: 'Toggle this value')
    //     choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
    //     password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    // }

    // this is build section
    stages {
        stage('Read Version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    env.appVersion = packageJson.version
                    echo "appVersion: ${env.appVersion}"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh 'npm install'
                }
            }
        }
        stage('Unit Tests') {
            steps {
                script {
                    sh 'npm test'
                }
            }
        }
        stage('Sonar Scan') {
            environment {
                def scannerHome = tool 'sonar-8.0'
            }
            steps {
                script {
                    withSonarQubeEnv('sonar-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
        stage('Build Image') {
            steps {
                script {
                    sh '''
                        docker build -t catalogue:1.0.0 .
                    '''
                }
            }
        }

        stage('Deploy') {
            when {
                expression { params.DEPLOY == true }
            }
            steps {
                input(
                    message: 'Should we continue?',
                    ok: 'Yes, we should.',
                    submitter: 'alice,bob',
                    parameters: [
                        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
                    ]
                )
            }
        }
    }
    post { 
        
        success {
            echo 'This will run only if successful'
        }
        
        failure {
            echo 'This will run only if failed'
        }   
        
        aborted { 
            echo 'Pipeline was aborted'
        }
    }
}