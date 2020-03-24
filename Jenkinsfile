
pipeline {
    agent any

    stages {
        stage('init') {
            steps {
                script {
                    def scmVars = checkout scm
                    env.PREVIOUS_SUCCESSFUL_COMMIT = scmVars.GIT_PREVIOUS_SUCCESSFUL_COMMIT
                }
            }
        }
        stage('project1') {
            when {
                expression {
                    matches = sh(returnStatus: true, script: "git diff --name-only $PREVIOUS_SUCCESSFUL_COMMIT|egrep -q '^project1'")
                    return !matches
                }
            }
            steps {
                build "../project1/$BRANCH_NAME"
            }
        }
        stage('echo'){
            steps {
                echo "branch: $BRANCH_NAME"
                echo "ok #12"
            }
        }
    }
}
