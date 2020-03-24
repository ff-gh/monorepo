
pipeline {
    agent any
    
    environment {
        project1Path = 'project1/'
        project2Path = 'project2/'
        project3Path = 'project3/'
    }

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
                            return !gitDiff(PREVIOUS_SUCCESSFUL_COMMIT, project1Path)
                        }
                    }
                    steps {
                        echo "building project 1"
                        buildProject(project1Path, BRANCH_NAME)
                    }
                }
                stage('project2') {
                    when {
                        expression {
                            return !gitDiff(PREVIOUS_SUCCESSFUL_COMMIT, project2Path)
                        }
                    }
                    steps {
                        echo "building project 2"
                        buildProject(project2Path, BRANCH_NAME)
                    }
                }
                stage('project3') {
                    when {
                        expression {
                            return !gitDiff(PREVIOUS_SUCCESSFUL_COMMIT, project3Path)
                        }
                    }
                    steps {
                        echo "building project 3"
                        buildProject(project3Path, BRANCH_NAME)
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

def gitDiff(String commit, String name) {
    return sh(returnStatus: true, script: "git diff --name-only $commit|egrep -q '^$name'")
}

def buildProject(String projectPath, String branchName) {
    def buildPath = "../" +  projectPath + branchName
    build buildPath
}