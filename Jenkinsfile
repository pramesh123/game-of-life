pipeline {
    agent { label 'ltecomm'}
    triggers {
        cron ('H * * * 1-5')
    }
    parameters {
        string (name: 'MAVEN_GOAL', defaultValue: 'package', description: 'parameter checking')
    }
    stages {
        stage('scm') {
            steps {
                git 'https://github.com/pramesh123/game-of-life.git'
            }
        }
        stage('build') {
            steps {
                sh script: "mvn ${param.MAVEN_GOAL}"
            }
        }
        stage('post build') {
            steps {
                junit 'gameoflife-web/target/surefire-reports/*.xml'
                archiveArtifacts 'gameoflife-web/target/*.war'
                stash name: 'warfile', includes: 'gameoflife-web/target/*.war'
            }
        }
        stage ('copy files to other node') {
            agent { label 'ltelog'}
            steps {
                unstash name: 'warfile'
            }
        }

    }
    post {
        always {
            mail to: 'rams.pattipaka@gmail.com',
            subject: "Status of pipeline ${currentBuild.fullDisplayName}" ,
            body: "${env.BUILD_URL} has result ${currentBuild.result}"
        }
    }
}