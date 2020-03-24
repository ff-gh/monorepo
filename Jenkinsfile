
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
        stage('parallel builds') {
            parallel {
                stage('project1') {
                    when {
                        expression {
                            matches = sh(returnStatus: true, script: "git diff --name-only $PREVIOUS_SUCCESSFUL_COMMIT|egrep -q '^project1'")
                            return !matches
                        }
                    }
                    steps {
                        echo "building project 1"
                        build "../project1/$BRANCH_NAME"
                    }
                }
                stage('project2') {
                    when {
                        expression {
                            matches = sh(returnStatus: true, script: "git diff --name-only $PREVIOUS_SUCCESSFUL_COMMIT|egrep -q '^project2'")
                            return !matches
                        }
                    }
                    steps {
                        echo "building project 2"
                        build "../project2/$BRANCH_NAME"
                    }
                }
                stage('project3') {
                    when {
                        expression {
                            matches = sh(returnStatus: true, script: "git diff --name-only $PREVIOUS_SUCCESSFUL_COMMIT|egrep -q '^project3'")
                            return !matches
                        }
                    }
                    steps {
                        echo "building project 3"
                        build "../project3/$BRANCH_NAME"
                    }
                }
            }
        }
        stage('wrap up'){
            steps {
                echo "branch: $BRANCH_NAME"
                echo "ok #12"
            }
        }
    }
}
