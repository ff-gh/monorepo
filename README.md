# Monlithic Repository
sample project for building a jenkins pipeline using a monolithic repository

- root jenkinsfile 
    - parse git commits for folders (projects) that changed
    - execute pipelines of the changed projects
- project specific jenkinsfile
    - execute pipeline for the corresponding project (in the subfolder)

# Features
- main jenkinsfile (at the root of the monorepo) can execute downstream builds according to what changed in the last commit
- parallel builds of the projects
- multibranch pipeline support

# Jenkins configuration
- create a folder for the monorepo
- inside the folder create a `Multibranch Pipeline` for each project
    - Branch Sources: configure the git repo to `Discover branches` and `Check out to matching local branch`
    - Build Configuration: change the `Script Path` to point to the path of each project's jenkinsfile
        - Example: For project1 the script path is `project1/Jenkinsfile`
- inside the folder create a `Multibranch Pipeline` for the main jenkinsfile (e.g githandler)
    - Branch Sources: configure the git repo to `Discover branches` and `Check out to matching local branch`
    - Build Configuration: ensure the `Script Path` points to the jenkinsfile in the root of the project
    - Ensure a webhook is configured to update this pipeline since it will be handling the commits
- for detailed configurations see the wiki: https://github.com/ff-gh/monorepo/wiki

