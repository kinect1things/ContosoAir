# ContosoAir
https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/

[![Pipeline](https://dev.azure.com/wardzen/Contoso%20Air/_apis/build/status/kinect1things.ContosoAir?branchName=master)](https://dev.azure.com/wardzen/Contoso%20Air/_build/latest?definitionId=2&branchName=master) 
[![Website](https://img.shields.io/website?url=https%3A%2F%2Fgithubci-web-contosoair.azurewebsites.net)](https://githubci-web-contosoair-asp.azurewebsites.net)
[![License](https://img.shields.io/github/license/kinect1things/ContosoAir)](https://github.com/kinect1things/ContosoAir/blob/master/LICENSE)

## Lab Scenario:
In this lab, we’ll be illustrating the integration and automation benefits of __Azure DevOps__. We will take on the role of helping a fictitious airline—Contoso Air—that has developed their flagship web site using Node.js. To improve their operations, they want to implement pipelines for continuous integration and continuous delivery so that they can quickly update their public services and take advantage of the full benefits of DevOps and the cloud.

The site will be hosted in Azure, and they want to automate the entire process so that they can spin up all the infrastructure needed to deploy and host the application without any manual intervention. Once this process is in place, it will free up their technology teams to focus more on generating business value.

## Exercise 1: Setting up automated CI/CD pipelines with Azure Pipelines
In this exercise, we will help Contoso Air revamp a critical component of their DevOps scenario. Like all airlines, they rely on their web site to generate and manage business opportunities. However, the current processes they have in place to move a change from their source code to their production systems is time-consuming and open to human error. They use GitHub to manage their source code and want to host their production site on Azure, so it will be our job to automate everything in the middle.

This will involve setting up a pipeline so that commits to the GitHub repo invoke a continuous integration build in Azure DevOps. Once that build is complete, it will invoke a continuous delivery deployment to push the bits out to Azure, creating the required resources, if necessary. The first thing we need to do is to connect GitHub with Azure DevOps, which we can do via the __Azure Pipelines__ extension in the GitHub Marketplace.

## Task 1: Installing Azure Pipelines from GitHub Marketplace
__Azure Pipelines__ is available in GitHub Marketplace which makes it even easier for teams to configure a CI/CD pipeline for any application using your preferred language and framework as part of your GitHub workflow in just a few simple steps:

1. Switch to the browser tab open to the root of your GitHub fork.

2. Navigate to the __GitHub Marketplace__.
    
    ![marketplace-github](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/marketplace-github.png)
3. Search for “pipelines” and click Azure Pipelines.
    
    ![azurepipelines-extension](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/azurepipelines-extension.png)

4. Scroll to the bottom and click __Install it for free__. If you previously installed Azure Pipelines, select __Configure access__ instead to skip steps 6-8.
    
    ![install-azurepipelines](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/install-azurepipelines.png)
    
    > Note: Azure Pipelines is free to use for both public and private repos. You get unlimited build minutes and 10 free parallel jobs for public repositories. For private repos, you get 1 free parallel job and 1800 minutes per month. If you have a need to scale your builds, you can add parallel job support for a nominal fee. 

5. If you have multiple __GitHub__ accounts, select the one you forked the project to from the __Switch billing account__ dropdown.

    ![switch-account](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/switch-account.png)

6. Click __Complete order and begin installation__.

    ![complete-order](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/complete-order.png)

7. Select the repositories you want to include (or __All repositories__) and click Install.

    > Note: If you’ve previously installed Azure Pipelines, you may need to toggle between the __All__ and __Select__ radio buttons to enable the wizard in Task 2. You can always create the pipeline directly from Azure Pipelines if the wizard does not appear.

    ![install](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/install.png)

## Task 2: Configuring a Continuous Integration Pipeline
Now that Azure Pipelines has been installed and configured, we can start building the pipelines but we will need to select a project where the pipeline will be saved. You may select an existing or create a new Azure DevOps project to hold and run the pipelines we need for continuous integration and continuous delivery. The first thing we’ll do is to create a CI pipeline.

1. Select the organization and Azure DevOps project that you want to use. If you do not have one, you can create for free.

    ![setup-azurepipelines](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/setup-azurepipelines.png)

2. Select the forked repo.

    ![select-forkedrepo](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/select-forkedrepo.png)

    Every build pipeline is simply a set of tasks. Whether it’s copying files, compiling the source, or publishing artifacts, the existing library of tasks covers the vast majority of scenarios. You can even create your own if you have specialized needs not already covered. We’re going to use YAML, a markup syntax that lends itself well to describing the build pipeline. Note that the Node.js pipeline as a starting point based on an analysis of our source project. We’ll replace the contents with the final YAML required for our project.

3. Select __Node.JS__ as the recommended template if prompted.

    ![nodejs-template](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/nodejs-template.png)

4. Replace the default template with the YAML below.

    ```node.js
    pool:
      vmImage: 'ubuntu-16.04'
    trigger:
      - master
    steps:
      - task: CopyFiles@2
        displayName: 'Copy Files to: $(build.artifactstagingdirectory)/Templates'
        inputs:
          SourceFolder: deployment
          Contents: '*.json'
          TargetFolder: '$(build.artifactstagingdirectory)/Templates'
      - task: Npm@1
        displayName: 'npm custom'
        inputs:
          command: custom
          verbose: false
          customCommand: 'install --production'
      - task: ArchiveFiles@2
        displayName: 'Archive $(Build.SourcesDirectory)'
        inputs:
          rootFolderOrFile: '$(Build.SourcesDirectory)'
          includeRootFolder: false
      - task: PublishBuildArtifacts@1
        displayName: 'Publish Artifact: drop'
    ```

    > Note: YAML is very strict with indentation. If you are new to YAML, we would recommend that you use tools to format and validate the code. There are several free tools available on the web.

5. Click Save and run.

    ![saveandrun](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/saveandrun.png)

6. Confirm the __Save and run__ to commit the YAML definition directly to the master branch of the repo.

    ![saveandrun2](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/saveandrun2.png)

7. Follow the build through to completion.

    ![build1](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/build1.png)

## Task 3: Adding a build status badge
An important sign for a quality project is its build status badge. When someone finds a project that has a badge indicating that the project is currently in a successful build state, it’s a sign that the project is maintained effectively.

1. Click the build pipeline to navigate to its overview page.

    ![buildpipeline](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/buildpipeline.png)

2. From the __ellipses (…)__ dropdown, select __Status badge__.

    ![statusbadge1](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/statusbadge1.png)

3. The __Status badge__ UI provides a quick and easy way to integrate the build status wherever you want. Often, you’ll want to use the provided URLs in your own dashboards, or you can use the Markdown snippet to add the status badge to locations such as Wiki pages. Click the __Copy to clipboard__ button for __Sample Markdown__.

    ![statusbadge2](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/statusbadge2.png)

4. Return to Visual Studio Code and open the __README.md__ file.

5. Paste in the clipboard contents at the beginning of the file. Press __Ctrl+S__ to save the file.

    ![statusbadge3](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/statusbadge3.png)

6. From the __Source Control__ tab, enter a commit message like __Added build status badge__ and press __Ctrl+Enter__ to commit. Confirm if prompted.

    ![statusbadge4](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/statusbadge4.png)

7. In Git, only changes need to be staged first to be included in the commit. If you are prompted to choose whether you want the VS Code automatically to stage all changes and commit them directly, choose __Always__

    ![commit-message](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/commit-message.png)

8. If you receive an error prompting you to configure user .name and user.email in git, open a command prompt and enter the following command to set your user name and email address:

    ```
    git config --global user.name "Your Name"

    git config --global user.email "Your Email Address"
    ```

9. Press the __Synchronize Changes__ button at the bottom of the window to push the commit to the server. Confirm if prompted.

    ![syncchanges](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/syncchanges.png)

10. You will need to sign in to GitHub if you have not already signed in

    ![signin-github](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/signin-github.png)

11. Go to the readme file on the browser and you will see the status.

    ![statusbadge-github](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/statusbadge-github.png)

## Task 4: Embedding automated tests in the CI pipeline
Now that we have our CI successfully built, it’s time to deploy but how do we know if the build is a good candidate for release? Most teams run automated tests, such as unit tests, as a part of their CI process to ensure that they are releasing a high-quality software. Teams capture key code metrics such as code coverage, code analysis, as they run the tests, to make sure that the code quality does not drop and the technical debt if not completely eliminated, is kept low.

We’re going to pull down the azure-pipelines.yml file that we created earlier and add tasks to run some tests and publish the test results.

1. Return to Visual Studio Code.

2. From the __Explorer__ tab, open __azure-pipelines.yml__.

    ![image42](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image42.png)

    Before we make our change, let’s take a quick look at the build tasks. There are four steps required for the build. First, deployment templates are copied to a target folder for use during the release process. Next, the project is built with NPM. After that, the built solution is archived and finally published for the release pipeline to access. With the Azure Pipelines extension for Visual Studio Code, you get a great YAML editing experience, including support for IntelliSense.

    hat we are missing is testing in the pipeline. We already have unit tests for our code. We just have to run them in the pipeline. We will add tasks to run the test and publish the results and code coverage.

3. Remove all the steps and replace it with the following code. Press __Ctrl+S__ to save the file.

    ```Node.js
    pool:
      vmImage: ubuntu-16.04
    trigger:
      - master
    steps:
      - task: Npm@1
        inputs:
          command: 'custom'
          customcommand: 'install --production'
      - script: |
          npm install
          npm test
        displayName: 'Run unit tests'
        continueOnError: true
      - task: PublishTestResults@2
        displayName: 'Publish Test Results'
        condition: succeededOrFailed()
        inputs:
          testResultsFiles: $(System.DefaultWorkingDirectory)/test-report.xml
      - task: PublishCodeCoverageResults@1
        displayName: 'Publish Code Coverage'
        condition: 'in(variables[''Agent.JobStatus''], ''Succeeded'')'
        inputs:
          codeCoverageTool: Cobertura
          summaryFileLocation: '$(System.DefaultWorkingDirectory)/coverage/*coverage.xml'
          reportDirectory: $(System.DefaultWorkingDirectory)/coverage
      - task: ArchiveFiles@2
        displayName: 'Archive sources'
        inputs:
          rootFolderOrFile: $(Build.SourcesDirectory)
          includeRootFolder: false
      - task: CopyFiles@2
        displayName: 'Copy ARM templates'
        inputs:
          SourceFolder: deployment
          Contents: '*.json'
          TargetFolder: $(build.artifactstagingdirectory)/Templates
      - task: PublishBuildArtifacts@1
        displayName: 'Publish Artifact: drop'
    ```

4. From the __Source Control__ tab, enter a commit message like __Updated build pipeline__ and press __Ctrl+Enter__ to commit. Confirm if prompted.

    ![image43](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image43.png)

5. Press the __Synchronize Changes__ button at the bottom of the window to push the commit to the server. Confirm if prompted.

    ![image44](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image44.png)

6. Back in Azure DevOps, navigate to __Pipelines –> Pipelines__. We can see that our build pipeline has kicked off a new build.

    ![updated-build](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/updated-build.png)

    We can follow as it executes the tasks we defined earlier, and even get a real-time view into what’s going on at each step. When the build completes, we can review the logs and any tests that were performed as part of the process. Track the build tasks.

    ![image47](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image47.png)

    Follow the build through to completion.

    ![image48](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image48.png)

7. Now that the build has completed, let’s check out the __Tests__ tab to view the published tests results. We can get quantitative metrics such as total test count, test pass percentage, failed test cases, etc., from the __Summary__ section

    ![image-tests1](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image-tests1.png)

8. The __Result__ section lists all tests executed and reported as part of the current build or release. The default view shows only the failed and aborted tests in order to focus on tests that require attention. However, you can choose other outcomes using the filters provided

    ![image-tests2](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image-tests2.png)

9. Finally, you can use the details pane to view additional information, for the selected test case, that can help to troubleshoot such as the error message, stack trace, attachments, work items, historical trend, and more.

From the results, we can see all 40 tests have passed which means we have not broken any changes and this build is a good candidate for deployment.

### Task 5: Configuring a CD pipeline with Azure Pipelines
Now that the build pipeline is complete and all tests have passed, we can turn our attention to creating a release pipeline.

Like the build templates, there are many packaged options available that cover common deployment scenarios, such as publishing to Azure. But to illustrate how flexible and productive the experience is, we will build this pipeline from an empty template.

1. From the left hand menu, under __Pipelines__ click __Releases__. Click __New Pipeline__ to create a new CD pipeline to deploy the artifacts produced by the build.

    ![image13](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image13.png)

2. Click Empty job.

    ![image14](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image14.png)
   
    The first item to define in a release pipeline is exactly what will be released and when. In our case, it’s the output generated from the build pipeline. Note that we could also assign a schedule, such as if we wanted to release the latest build every night.

3. Select the associated artifact.

    ![image15-1](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image15-1.png)

4. Set __Source__ to the build pipeline created earlier and __Default version__ to __Latest__. Change the __Source alias__, if you want, to something like “___ContosoAir-CI__” and click __Add__. Note that this is an identifier (typically a short name) that uniquely identifies an artifact linked to the release pipeline. It cannot contain the characters: \ / : * ? < > | or double quotes

    ![image16](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image16.png)

    As we did with continuous integration starting on a source commit, we also want to have this pipeline automatically start when the build pipeline completes. It’s just as easy.

5. Click the __Triggers__ button on the artifact.

    ![image17](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image17.png)

6. __Enable__ continuous deployment, if it is not already enabled.

    ![image18](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image18.png)

    We also have the option of adding quality gates to the release process. For example, we could require that a specific user or group approve a release before it continues, or that they approve it after it’s been deployed. These gates provide notifications to the necessary groups, as well as polling support if you’re automating the gates using something dynamic, such as an Azure function, REST API, work item query, and more. We won’t add any of that here, but we could easily come back and do it later on.

7. Click the __pre-deployment conditions__ button.

    ![image19](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image19.png)

8. Review pre-deployment condition options.

    ![image20](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image20.png)

9. Select the __Variables__ tab.

    ![image21](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image21.png)

    In this pipeline, we’re going to need to specify the same resource group in multiple tasks, so it’s a good practice to use a pipeline variable. We’ll add one here for the new Azure resource group we want to provision our resources to.

10. __Add__ a __resourcegroup__ variable that is not currently used by an existing resource group in your Azure account (“__contosoair__” will be used in this script).

    ![image22](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image22.png)

    Also, just like the build pipeline, the release pipeline is really just a set of tasks. There are many out-of-the-box tasks available, and you can build your own if needed. The first task our release requires is to set up the Azure deployment environment if it doesn’t yet exist. After we add the task, I can authorize access to the Azure account I want to deploy to and instruct it to use the variable name we just specified for the resource group name.

11. Select the __Tasks__ tab. Click the __Add task__ button.

    ![image23](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image23.png)

    ![image24](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image24.png)

12. Search for “__arm__” and __Add__ an __ARM template deployment__ task.

    ![image25](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image25.png)

13. Select the newly created task.

    ![image26](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image26.png)

    Then, select the __Task version__ to __2.*__.

14. Select and authorize an Azure subscription.

    ![image27](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image27.png)

    > Note: You will need to disable popup-blockers to sign in to Azure for authorization. If the pop-up window hangs, please close and try it again.

15. Set the __Resource group__ to ”__$(resourcegroup)__” and select a __Location__.

    ![image28](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image28.png)

    Rather than having to manually create the Azure resources required to host the web app, we defined an __Azure Resource Manager__ or __ARM__ template that describes the environment in JSON. This allows the environment definition to be updated and managed like any other source file. These were the files we copied to the Templates folder during the build pipeline. You can also override the template parameters as part of this configuration if required.

16. Enter the settings below. You can use the browse navigation to select them from the most recent build output.

    ![image29](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image29.png)

    ![image-release3](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image-release3.png)

    * Template: **$(System.DefaultWorkingDirectory)/_ContosoAir-CI/drop/Templates/azuredeploy.json**
    * Template parameters: **$(System.DefaultWorkingDirectory)/_ContosoAir-CI/drop/Templates/azuredeploy.parameters.json**

    You will also need to set __Override template parameters__ to generate an Azure app service name that is globally unique, so your name is recommended. For example, if your name is __John Doe__, use something like __-p_environment johndoe__. This will be used as part of the app service name in Azure, so please limit it to supported characters.

    When this task completes, it will have generated an Azure resource group with the resources required to run our application. However, the ARM template does some processing of the variables to generate names for the resources based on the input variables, which we will want to use in future tasks. While we could potentially hardcode those variables, it could introduce problems if changes are made in the future, so we’ll use the ARM Outputs task to retrieve those values and put them into pipeline variables for us to use. This task happens to be a 3rd party task I installed earlier from the Visual Studio Marketplace. It contains this and many other extensions for Azure DevOps from both Microsoft and 3rd parties.

17. Click the __Add task__ button.

    ![image24](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image24.png)

18. Search for “__arm__” and add __ARM outputs__ task.

    ![armoutputs](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/armoutputs.png)

    The key variable we care about here is the name of the app service created, which our ARM template has specified as an output. This task will populate it for us to use as the “web” variable in the next task.

19. Select the newly created task. Select the same subscription from the previous task and enter the same resource group variable name.

    ![armoutputs2](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/armoutputs2.png)

    Finally, we can deploy the app service. We’ll use the same subscription as earlier and specify the web variable as the name of the app service we want to deploy to. By this time in the pipeline, it will have been filled in for us by the ARM Outputs task. Also, note that we have the option to specify a slot to deploy to, but that is not covered in this demo.

20. Click the __Add task__ button.

    ![image24](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image24.png)

    Search for “__app service__ and __Add__ an __Azure App Service Deploy__ task.

    ![image36](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image36.png)

21. Select the newly created task.

    ![image37](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image37.png)

22. Select the same subscription as earlier.

    ![image38](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image38.png)

23. Enter the __App Service name__ of ”__$(web)__”.

    ![image39](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image39.png)

24. __Save__ the pipeline.

    ![image40](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image40.png)

25. Select + __Release__ and then select __Create a Release__

    ![image-release1](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image-release1.png)

26. Select Create to start a new release.

27. Navigate to the release summary by clicking on the __Release-1__ link that appears. Click __In progress__ to follow the release process.

    ![image51](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image51.png)

28. Note that it will take a few minutes (around 5 at the time of drafting) for the app to finish deploying due to heavy first-time operations.

    ![image52](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image52.png)

29. Select the __App Service Deploy__ task to view the detailed log. You should find the URL to the published website here. __Ctrl+Click__ the link to open it in a separate tab.

    ![image-release2](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image-release2.png)

30. This will open the web page of the Contoso Air.

    ![image-contoso](https://azuredevopslabs.com/labs/vstsextend/github-azurepipelines/images/image-contoso.png)

## Next: GitHub integration with Azure Boards
In addition to Azure Pipelines, GitHub users can also benefit from [Azure Boards](https://azure.microsoft.com/services/devops/boards/), a set of features that enable you to plan, track, and discuss work across your teams using Kanban boards, backlogs, team dashboards, and custom reporting. You can link GitHub activities from Azure Boards by mentioning them in commits and pull requests, and even automate the state transition of linked work items when pull requests are approved.

Continue to [GitHub integration with Azure Boards lab](https://azuredevopslabs.com/labs/vstsextend/github-azureboards)