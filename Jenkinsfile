import org.jenkinsci.plugins.pipeline.modeldefinition.Utils
import org.jenkinsci.plugins.workflow.multibranch.WorkflowMultiBranchProject
import groovy.transform.Field

@Field
final def JENKINS_FOLDER_NAME = "monorepo"
@Field
def parallelBuilds = [:]

node('app-server') {
    stage('Init') {
            echo "Checking out git repository"
            def scmVars = checkout scm
            echo "${scmVars}"
            sh 'printenv'

            def filteredJobs = getFilteredJobs()
            echo "Filtered Jobs: ${filteredJobs}"

            echo "Building parallel builds map"
            parallelBuilds = filteredJobs.collectEntries{
                [ "${it.buildName}" : generateBuildStage(it.buildName, it.jobPath, it.jobScriptPath) ]
            }
            filteredJobs = null
            echo "Parallel builds map: ${parallelBuilds}"
    }
    
    parallel parallelBuilds

    stage('wrap up'){
            parallelBuilds = null
            echo "branch: $BRANCH_NAME"
            echo "ok #12"
    }
}

def gitDiff(String commit, String name) {
    echo "Diffing commit: ${commit} for project ${name}"
    return sh(returnStatus: true, script: "git diff --name-only $commit|egrep -q '^$name'")
}

def generateBuildStage(String buildName, String jobPath, String projectPath) {
    echo "Generating build stage for '${buildName}', '${jobPath}', '${projectPath}'"
    return {
        stage("Building: ${buildName}") {
            // if(gitDiff("${GIT_PREVIOUS_COMMIT}", projectPath)){
                build jobPath
            // } else {
            //     echo "Skipped stage ${buildName}"
            //     Utils.markStageSkippedForConditional(STAGE_NAME)
            // }
        }
    }
}

/**
 * Gets a list of downstream jobs.
 *
 * Searches the Jenkins instance for all available jobs. Filters out the current job, filters out those that are not
 * within the folder specified by JENKINS_FOLDER_NAME, and only collects those with the same name as this job. 
 * 
 * A folder in this context is one created in Jenkins to hold multiple pipelines. It does not refer to any filesystem
 * or source control system.
 *
 * The names are matched in order to run specific branches of a monorepo. For example, running the 'develop' branch
 * of this pipeline should only run the 'develop' branche of the downstream projects.
 */
def getFilteredJobs() {
    echo "Getting filtered jobs data"
    return Jenkins.instance.getAllItems(Job.class).findAll{
        (it.fullName != "${JOB_NAME}") &&                       //Don't get current job
        (it.fullName.split('/')[0] == JENKINS_FOLDER_NAME) &&   //Only get those in folder
        (it.name == BRANCH_NAME)                                //Only get matching branches
    }.collect{
        def buildName = it.fullName.split('/')[1]
        def jobScriptPath = it.definition.scriptPath.split("/")[0]

        ["buildName" : buildName, "jobScriptPath" : jobScriptPath, "jobPath" : it.fullName]
    }
}