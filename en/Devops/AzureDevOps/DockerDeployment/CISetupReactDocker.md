# Docker Setup for React app in Azure DevOps

> Prerequisite - [Docker Desktop](https://docs.docker.com/desktop/windows/install/) setup should be completed in your machine

## `Step 1 : Add Dockerfile`:


![Alt text](../DockerDeployment/_images/DockerfileInRoot.png)

Create a Dockerfile and paste the code shown below:

```
FROM node:16.14.2
WORKDIR /app
COPY package.json ./
COPY package-lock.json ./
COPY ./ ./
RUN npm i
CMD ["npm", "run", "start"]
```

> The node version should not be `alpine`.

## `Step 2 : Add `Docker Registry` Connection in your Azure DevOps Project`:

Go to `Service Connection` in your project settings and add a new Service Connection `Docker Registry` and configure as shown below:

![Alt text](../DockerDeployment/_images/DockerServiceConnection1.png)

![Alt text](../DockerDeployment/_images/DockerServiceConnection2.png)

## `Step 3 : Setup DevOps Agent`:

DevOps Agent of your project should be set in the machine that has `Docker Desktop`.

> To know about Agent Setup , [Click here](/en/Devops/AzureDevOps/CISetupDotNet.md#2-set-up-an-azure-pipelines-agent)

## `Step 4 : Add Docker Tasks in your pipeline`:

### (i) Add `Build an image` Docker Task:

Configure this task as shown below:

- Link the `Docker Registry Service Connection` we have created before here:

    ![Alt text](../DockerDeployment/_images/BuildImage1.png)

- Select `Build an image` Action:

    ![Alt text](../DockerDeployment/_images/BuildImage2.png)

- Configure Advanced options as shown below:

    ![Alt text](../DockerDeployment/_images/BuildImage3.png)

- Configure Control options as shown below:

    ![Alt text](../DockerDeployment/_images/BuildImage4.png)

### (ii) Add `Run an image` Docker Task:

Configure this task as shown below:

- Link the `Docker Registry Service Connection` we have created before here:

    ![Alt text](../DockerDeployment/_images/RunImage1.png)

- Select `Run an image` Action:

    ![Alt text](../DockerDeployment/_images/RunImage2.png)

- Configure `Run an image` Action as shown below:

    ![Alt text](../DockerDeployment/_images/RunImage3.png)

- Configure Advanced options as shown below:

    ![Alt text](../DockerDeployment/_images/RunImage4.png)

- Configure Control options as shown below:

    ![Alt text](../DockerDeployment/_images/RunImage5.png)

## `Step 5 : Run Pipeline and view the Container in your Docker Desktop`:

- Open Docker Desktop after successfull run of the pipeline.

- The Container Created from our pipline run is seen:

    ![Alt text](../DockerDeployment/_images/ProjectContainer.png)

- Open the Container in Browser:

    ![Alt text](../DockerDeployment/_images/DockerOpenInBrowser.png)

- The hosted project is running successfully:

    ![Alt text](../DockerDeployment/_images/HostedSite.png)
 


