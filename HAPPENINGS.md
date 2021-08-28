# Things that happened during the setup/deployment

1. Had issue during Task 1 Step 7 setup where Azure DevOps threw error saying 'no parallelism has been purchased or granted'
    ![devops-error1](https://raw.githubusercontent.com/kinect1things/ContosoAir/master/images/devops-error1.png)
    * MS had me fill out a form that asked me about my project and what i was using it for
    * Fixed by using agents and adding the nodejs and npm packages to my client then adding the agent capability

2. The pipleline will now start and iterates through the steps outlined in the yaml file but i'm getting an error on the ArchiveFiles@2 task
    * ##[error]Error: Unable to locate executable file: 'zip'. Please verify either the file path exists or the file can be found within a directory specified by the PATH environment variable. Also check the file mode to verify the file is executable.
    ![devops-error2](https://raw.githubusercontent.com/kinect1things/ContosoAir/master/images/devops-error2.png)