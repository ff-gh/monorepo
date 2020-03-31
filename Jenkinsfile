
pipeline {
    agent {
        label 'app-server'
    }
    
    environment {
        project1JenkinsPath = 'Project 1/'
        project2JenkinsPath = 'Project 2/'
        project3JenkinsPath = 'Project 3/'
        
        project1RepoPath = 'project1/'
        project2RepoPath = 'project2/'
        project3RepoPath = 'project3/'
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
                            return !gitDiff(PREVIOUS_SUCCESSFUL_COMMIT, project1RepoPath)
                        }
                    }
                    steps {
                        echo "building project 1"

                        buildProject(project1JenkinsPath, BRANCH_NAME)
                    }
                }
                stage('project2') {
                    when {
                        expression {
                            return !gitDiff(PREVIOUS_SUCCESSFUL_COMMIT, project2RepoPath)
                        }
                    }
                    steps {
                        echo "building project 2"
                        buildProject(project2JenkinsPath, BRANCH_NAME)
                    }
                }
                stage('project3') {
                    when {
                        expression {
                            return !gitDiff(PREVIOUS_SUCCESSFUL_COMMIT, project3RepoPath)
                        }
                    }
                    steps {
                        echo "building project 3"
                        buildProject(project3JenkinsPath, BRANCH_NAME)
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
    echo "Diffing git info for commit " + commit + " for project " + name
    // temp, show the diffs
    sh(script: "git diff --name-only $commit")
    return sh(returnStatus: true, script: "git diff --name-only $commit|egrep -q '^$name'")
}

def buildProject(String projectPath, String branchName) {
    echo "Building Project: " + projectPath + ", on branch: " + branchName
    //def buildPath = "../monorepo_" +  projectPath + "_" + branchName
    def buildPath = "../" +  projectPath + branchName

    echo "Initializing build: " + buildPath
    build buildPath
}
