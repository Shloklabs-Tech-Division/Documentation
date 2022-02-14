# AzureDevops CI/CD Environment Setup 
## `.Net Framework Project`
## 1 : Create a project in Azure DevOps
- step 1 : Select Azure DevOps logo Azure DevOps to open the Projects page.
- step 2 : Choose the organization, and then select New project.

    ![Alt text](../AzureDevOps/_images/projects-hub-select-new-project.png)

- step 3 : Enter information into the form provided.

    ![Alt text](../AzureDevOps/_images/CreateProject.png)

- step 4 : Select Create. The welcome page appears.

    ![Alt text](../AzureDevOps/_images/ProjectDashboard.png)

## 2 : Set up an Azure Pipelines agent

To execute an Azure DevOps pipeline, you need an Azure Pipelines agent.

- Step 1 : Download an agent

    - In the bottom left of your Azure DevOps project, `click Project settings`.

        ![Alt text](../AzureDevOps/_images/ProjectSettings.png)

    - `Click Agent pools`.

        ![Alt text](../AzureDevOps/_images/AgentPool.png)
        
    - Select an available agent pool. You can select from a hosted pool or from a local pool (default). Or `Add new Pool`
    
        ![Alt text](../AzureDevOps/_images/NewAgentPool.png)
    
    - In the agent view that appears, `click New agent`.

        ![Alt text](../AzureDevOps/_images/InsideAgentPool.png)

    - Select Windows as OS, your OS’s architecture (x64 or x86), and `click Download`.

        ![Alt text](../AzureDevOps/_images/NewAgent.png)

    - `Unpack` the downloaded .zip file to a folder of your choice.

        ![Alt text](../AzureDevOps/_images/AgentZip.png)

        The unpacked agent files.

- Step 2 : Configure the downloaded agent:


    - `Double-click` the file `config.cmd`.

    - `Configure` the agent as follows:

        ![Alt text](../AzureDevOps/_images/ConfigureAgent.png)

    - `Paste` the Azure-DevOps project URL from your browser’s address bar.

    - `Press Enter` to use PAT as authentication type.

    - In the Security menu of your Azure DevOps account, `create` a full-access [PAT](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page) and `paste` it here.
    
    !>  The generated PAT is visible only once and can’t be recreated. Copy and paste it immediately after generating it.



    - `Press Enter` to select the default local pool.

    - `Press Enter` to confirm the agent’s name or change it.

    - `Press Y` if you have previously had an agent installed on this machine; `press Enter` if not.

    - `Press Enter` to confirm the work folder or change it.

    - `Press N`. The agent must not be run as a service.

    - `Press Y`.

- Step 3 : Start the agent

    - In the folder to which you unpacked the agent files, double-click `run.cmd`.

        ![Alt text](../AzureDevOps/_images/RunAgent.png)

    The agent starts, connects to the server, and idles until it receives a pipeline to execute.


## 4 : Import Source Code

## a. By Creating a service connection:

To Create a standard service connection to a `SourceSafe` repository.

- Select `Project settings` > `Service connections`.

    ![Alt text](../AzureDevOps/_images/ServiceConnection.png)

- Select `+ New service connection`, select the type of service connection that you need ("`Other Git`" in our case), and then select `Next`.

    ![Alt text](../AzureDevOps/_images/OtherGit.png)

- Choose an authentication method, and then select Next.

    ![Alt text](../AzureDevOps/_images/ServiceConnectionAuthentication.png)

> **How to get Clone URL From Source Safe**
- To get "`Git Repository URL`" from SourceSafe Portal
    
    - Go to `SourceSafe Portal` and Navigate to the `Desired Project`.

    - Click `Code` tab and select `Clone` , Which will give a Git Repository URL.

    - Copy and paste it in `Edit Service Connection` Form of `Azure DevOps` Shown Above.

        ![Alt text](../AzureDevOps/_images/SourceSafePortal.png)

- Save the Connection.

## b. By Importing : 

- Go to `Repos` of your DevOps Project and Click `Import` option

    ![Alt text](../AzureDevOps/_images/ReposImport.png)

- Enter the Details and Hit Import

    ![Alt text](../AzureDevOps/_images/ImportReposForm.png)

    - To get `Clone URL` follow the steps shown above in `How to get Clone URL From Source Safe` section
    - We can give either *USERNAME* and *PASSWORD* or *USERNAME* and *PAT* (**P**ersonal **A**ccess **T**oken).

    - After Import the Source Code will be shown like this : 

        ![Alt text](../AzureDevOps/_images/SourceInRepos.png)


> **`How To Get Personal Access Token from SourceSafe Portal`**

- step 1: Go to `SourceSafe portal` and select `Security` Option.

![Alt text](../AzureDevOps/_images/SecuritySourceSafe.png)

- step 2: In Security Tab , Go to `Personal Access Token` and Click `Add`.

![Alt text](../AzureDevOps/_images/SecurityTabSourceSafe.png)

- step 3: Enter the Description & Select Duration

![Alt text](../AzureDevOps/_images/CreatePAT.png)

![Alt text](../AzureDevOps/_images/CreatePAT1.png)

- step 4: Copy the PAT and store it for next use.

## 5 : Create your first pipeline:

## a. By Service Connection:

- Step 1 : Go to `Pipeline` and click `New Pipeline`.

![Alt text](../AzureDevOps/_images/NewPipeline.png)

- step 2 : Choose `Other Git`.

![Alt text](../AzureDevOps/_images/OtherGitPipeline.png)

- step 3 : Select `Other Git` Again and the Connection which we created earlier, branch and Hit `Continue`.

![Alt text](../AzureDevOps/_images/OtherGitConnection.png)

- step 4 : Select suitable `Template` according to the Source

![Alt text](../AzureDevOps/_images/SelectTemplate.png)

- step 5 : Select Agent which we created earlier.

![Alt text](../AzureDevOps/_images/ChooseAgent.png)

- step 6 : Select Save & Queue.

## b. With Azure Repos Git:

- Step 1 : Go to `Pipeline` and click `New Pipeline`.

![Alt text](../AzureDevOps/_images/NewPipeline.png)

- step 2 : Choose `Azure Repos Git`.

![Alt text](../AzureDevOps/_images/AzureReposGit.png)

- step 3 : Select a Repository Which we imported into `Repos` earlier.

![Alt text](../AzureDevOps/_images/SelectRepository.png)

- step 4 : configure pipeline according to the source code.
 
![Alt text](../AzureDevOps/_images/ConfigurePipeline.png)

- step 5 : Review the YAML File show and Change the `Pool Name` to the `Agent Pool` we created earlier.
click Save and Run.

![Alt text](../AzureDevOps/_images/ReviewYaml.png)

Once the Pipeline is Saved in either way, running the Pipline will give us the following results:

![Alt text](../AzureDevOps/_images/InsidePipeline.png)

Click on Job to view the Results

![Alt text](../AzureDevOps/_images/AgentJobRun.png)

