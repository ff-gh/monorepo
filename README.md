# monlithic repository
sample project for building a jenkins pipeline with a monolithic repository

- root jenkinsfile 
    - parse git commits for folders (projects) that changed
    - execute pipelines of the changed projects
- project specific jenkinsfile
    - execute pipeline for the corresponding project (in the subfolder)
