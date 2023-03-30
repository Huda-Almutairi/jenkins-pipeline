

pipeline {
    agent any
    options{
    skipDefaultCheckout()
    }
    
    stages {
        stage('checkout scm') {
            steps {
                script {
                    git branch: 'main', credentialsId: 'GitHub-credentials', url: 'http://github.com/Huda-Almutairi/jenkins-pipeline.git'
                    sh 'git log  --pretty=format:"%h %s" | awk \'{print $0}\' ORS=\'\\n\' >>log.txt'
                }
            }
        }
        stage('get build Params User Input') {
            steps{
                script{
                    liste = readFile 'log.txt'
                    echo "please click on the link here to chose the branch to build"
                    env.COMMIT_SCOPE = input message: 'Please choose the branch to build ', ok: 'Validate!',
                            parameters: [choice(name: 'COMMIT', choices: "${liste}", description: 'Branch to build?')]
                }
            }
        } 
        stage("checkout the branch") {
            steps {
                script {
                    echo "${env.COMMIT_SCOPE}"
                    git branch: 'main', credentialsId: 'GitHub-credentials', url: 'http://github.com/Huda-Almutairi/jenkins-pipeline.git'
                    sh '''
                        git checkout ${env.COMMIT_SCOPE}
                        git checkout -b NEW_BRANCH_NAME
                        //git branch deploy-branch2 ${env.COMMIT_SCOPE}
                        //git checkout -b deploy-branch2
                        git add .
                        git commit -m 'new breanch from commit'
                        git push origin NEW_BRANCH_NAME
                    '''
                }                
            }
        }
        stage("exec maven build") {
            steps {
                withMaven(maven: 'M3', mavenSettingsConfig: 'mvn-setting-xml') {
                   sh "mvn clean install "
                }
            }
        }
        stage("clean workwpace") {
            steps {
                cleanWs()
            }
        }
    }
}
