def COMMIT_ID = ''
node('k8sagent') {
    properties([
        buildDiscarder(logRotator(numToKeepStr: '3', daysToKeepStr: '3')),
        pipelineTriggers([
            pollSCM('* * * * *')
        ])
    ])
    stage('Get The Golang project') {
        git 'https://github.com/ngostal2019/simpleGoApp.git'
        container('jnlp') {
            stage('List content of Go project') {
                COMMIT_ID = sh returnStdout: true, script: 'git log -n 1 --pretty=format:%H | cut -c1-5'
                sh """
                ls -ltha
                pwd
                echo $COMMIT_ID
                """
            }
        }
    }
    stage('Kaniko builds & Pushes To Docker') {
        container('kaniko'){
            sh '''
            ls -ltah /kaniko/.docker
            echo $DOCKER_CRED > /kaniko/.docker/config.json
            cat /kaniko/.docker/config.json
            '''
            sh """
            /kaniko/executor --context `pwd` --destination ngostal/golang:$COMMIT_ID
            /kaniko/executor --context `pwd` --destination ngostal/golang:latest
            """
        }
    }
    stage('Obtained the ArgoCD repository From GitHub') {
        cleanWs()
        git(
            url: 'https://github.com/ngostal2019/ArgoCD-manifest.git',
            branch: 'main',
            changelog: true,
            poll: true
            )
        container('jnlp') {
            stage('Local Update of ArgoCD repository deployment file') {
                sh """
                ls -ltha
                """
                def initDeployment = readFile 'overlays/dev/dev-deployment.yml'
                deployment = initDeployment.replaceAll("image:.*", "image: ngostal/golang:$COMMIT_ID")
                writeFile file: "overlays/dev/dev-deployment.yml", text: deployment
                sh "cat overlays/dev/dev-deployment.yml"
            }
        }
    }
    stage('Update ArgoCD Deployment file on GitHub') {
        container('jnlp') {
            sh """
            ls -ltha
            git remote -v
            git checkout -b hotfix/dev
            git status
            git add overlays/dev/dev-deployment.yml
            git config --global user.name "Jenkins Server"
            git config --global user.email "jenkins-server-${env.BUILD_ID}@hotmail.com"
            git config --list | grep "user"
            git commit -m "Updated dev-deployment.yml file from Jenkins CI."
            """
            withCredentials([gitUsernamePassword(credentialsId: 'GITHUB_PASS', gitToolName: 'Default')]) {
                sh 'git push -f origin hotfix/dev'
            }
        }
    }
}