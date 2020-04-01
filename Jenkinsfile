import org.jenkinsci.plugins.pipeline.modeldefinition.Utils

def buildfiles = []
node {
    stage('init') {
            def scmVars = checkout scm
            env.PREVIOUS_SUCCESSFUL_COMMIT = scmVars.GIT_PREVIOUS_SUCCESSFUL_COMMIT
            def gradleFiles = findFiles(glob: '**/build.gradle')
            gradleFiles.each {
                println "gradlefile ${it.path} - directory ${it.directory} - name ${it.name}"
            }
            def npmPackageFiles = findFiles(glob: '**/package.json')
            npmPackageFiles.each {
                println "package.json file ${it}"
            }
            buildfiles.addAll(gradleFiles)
            buildfiles.addAll(npmPackageFiles)
    }
    stage('parallel builds stage') {
            def parallelStagesMap = buildfiles.collectEntries { buildfile ->
                ["${buildfile.path}" : generateBuildStage(buildfile.getPath().take(buildfile.getPath().length()-buildfile.getName().length()), PREVIOUS_SUCCESSFUL_COMMIT)]
            }
            parallel parallelStagesMap
    }
    stage('wrap up'){
            echo "branch: $BRANCH_NAME"
            echo "ok #12"
    }
}

def gitDiff(String commit, String name) {
    return sh(returnStatus: true, script: "git diff --name-only $commit|egrep -q '^$name'")
}

def buildProject(String projectPath, String branchName) {
    def buildPath = "../" +  projectPath + branchName
    build buildPath
}

def generateBuildStage(String projectPath, def previousSuccessfulCommit) {
    return {
        stage("Building: $projectPath", !gitDiff(previousSuccessfulCommit /*PREVIOUS_SUCCESSFUL_COMMIT*/, projectPath)) {
            // when {
            //     expression {
            //         return !gitDiff(previousSuccessfulCommit /*PREVIOUS_SUCCESSFUL_COMMIT*/, 
            //             projectPath)
            //     }
            // }
            // steps {
            echo "building $projectPath"
            buildProject(projectPath, "${env.BRANCH_NAME}")
            
            //echo "projectPath $projectPath - previousSuccessfulCommit $previousSuccessfulCommit"
            //sh script: "sleep 30"
            //echo "End of building $project"
        }
    }
}

// override stage method to implement the skipping of stages
def stage(name, execute, block) {
    return stage(name, execute ? block : {
        echo "skipped stage $name"
        Utils.markStageSkippedForConditional(STAGE_NAME)
    })
}
