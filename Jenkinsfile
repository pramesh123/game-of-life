pipeline {
    agent { label 'ltecomm'}
    triggers {
        cron('H * * * 1-5')
    }
    parameters {
        string(name: 'MAVENGOAL', defaultValue: 'clean package', description: 'Enter your maven goal')
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
    }
    stages {
        stage('scm') {
            steps {
                git branch: 'dev', url: 'https://github.com/wakaleo/game-of-life.git'        
            }
        }
        stage('build') {
            steps {
                sh script: "mvn ${params.MAVENGOAL}"
            }
        }
        stage('post build') {
            steps {
                junit 'gameoflife-web/target/surefire-reports/*.xml'
                archiveArtifacts 'gameoflife-web/target/*.war'
                stash name: 'warfile', includes: 'gameoflife-web/target/*.war'
            }
        }
        stage ('copy to other node') {
            agent { label 'ltelog' }
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