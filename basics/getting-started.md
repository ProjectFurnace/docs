# Getting started

## DISCLAIMER 

***Furnace will deploy resources outside of the free tier on your cloud platform.  We are not responsible for any costs incurred***


## Prerequisites

Our aim is to have you up and running in less than 5 minutes. You'll need to meet the following prerequisites \(see current limitations\):

* Node 8.x LTS and NPM 6.x \([https://nodejs.org](https://nodejs.org/)\)
* A GitHub account and Personal Access Token \([https://github.com/settings/tokens](https://github.com/settings/tokens)\)

Further prerequisites are needed depending on which platform you will be deploying to.

#### For AWS: 
* An AWS Account \([https://aws.amazon.com/](https://aws.amazon.com/)\)
* AWS CLI and a configured profile \([https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\)

#### For Google Cloud Platform 
* A GCP Account \([https://cloud.google.com/](https://cloud.google.com/)\)
* A GCP Project \([https://console.cloud.google.com/cloud-resource-manager](https://console.cloud.google.com/cloud-resource-manager)\)
* A GCP Service Account file setup locally \([https://cloud.google.com/docs/authentication/getting-started](https://cloud.google.com/docs/authentication/getting-started)\)

To list projects already available in GCP if you have them run `gcloud projects list` from the gcloud CLI

#### For Azure 
* An Azure Subscription ID \([https://azure.microsoft.com/](https://azure.microsoft.com/)\)
* Azure Function Core Tools \([https://github.com/Azure/azure-functions-core-tools](https://github.com/Azure/azure-functions-core-tools)\)
* .NET Core SDK \([https://www.microsoft.com/net/download](https://www.microsoft.com/net/download)\)

To find your subscription ID run `az account show --query id -o tsv` from the Azure CLI

## Ignite the Furnace
The process starts with installing the Furnace CLI and running `furnace ignite`

```text
npm install @project-furnace/furnace-cli -g
furnace ignite
```

You'll be asked a series of questions relating to your environment. After filling in the questions Furnace will start creating various resources in your chosen platform.
These resources will consist of a deployment container, API endpoints, serverless functions, credentials and keys, storage, and message queues. 
For a more indepth look at what is actually being created please see the relevant platform specific documentation.

- [AWS](./system-architecture/aws-overview.md)
- [Azure](./system-architecture/azure-overview.md)
- [GCP](./system-architecture/gcp-overview.md)

This may take a few minutes to complete but when it's done you will be notified via the CLI.    
Once this command has completed you're ready to create a new stack.

```text
furnace new my-stack
cd my-stack
```

Again you'll be prompted to answer a series of questions about your Stack.  Furnace will then initialise a few more resources 
required for your Stack including GitHub repositories and webhooks.  You'll now have a new repository created that 
you can start to build on. You can learn more about working with Furnace Stacks with the 5 Minute Walkthrough.

The `stack.yaml` contains a list of environments, by default you have `dev`, `staging`, `production`. 
Once you're ready deploy your first stack, we do so using a standard `git` flow.

```text
git add .
git commit -am "initial commit"
git push origin master
```

This pushes to the first environment defined in your Stack, you can see the deployment status in `environments` 
in the Stack repository in GitHub or using the `furnace status` command inside the Stack directory.  

If something goes wrong or a deployment doesn't complete please see the platform specific page on where to find the relevant logs.

- [AWS](./system-architecture/aws-overview.md#log-locations)
- [Azure](./system-architecture/azure-overview.md#log-locations)
- [GCP](./system-architecture/gcp-overview.md#log-locations)

Once you're happy that your Stack is functioning how you expected in your initial environment you can `promote` 
it to an upstream environment eventually towards its final/production environment. For example, keeping with the 
default environments, to promote the `dev` environment to `staging` you would simply:

```text
furnace promote dev
```

## Ignite Command line arguments

`furnace ignite` can also be run with the following command line arguments:

#### Global args

These args can be used for any target platform

| Command  | Full  | Alias   | Type   | Default Value   | Description |
|---|---|---|---|---|---|
| Platform selection  | --platform   | -p   | String   | null | Which platform you will deploy to `aws`, `azure` or `gcp` |
| Instance Name  | --name  | -i  | String  | null | The name to give your Furnace Instance |
| Git Provider  | --gitProvider  | -g   | String  | null | Git provider being used (currently only supporting `github`) |
| Git Token | --gitToken | -t | String | null | Github access token |
| Store Git Token | --storeGitHubToken | | Boolean | true | Whether to store the Github token in the config |
| Location | --location| -l | String | null | Region deploying to. ie `us-west-1` or `uksouth` |

#### Platform Specific Args

These args are platform dependent: 

##### AWS
| Command  | Full  | Alias   | Type   | Default Value   | Description |
|---|---|---|---|---|---|
| Profile | --profile | -x | String | null | Local AWS Profile to use |
| Access Key Id | --accessKeyId | -a | String | null | AWS API Access Key |
| Secret Access Key | --secretAccessKey | -p | String | null| AWS API Access Secret |

##### Azure
| Command  | Full  | Alias   | Type   | Default Value   | Description |
|---|---|---|---|---|---|
| Subscription ID| --subscriptionId|-s|String|null| Azure Subscription ID |

##### GCP
| Command  | Full  | Alias   | Type   | Default Value   | Description |
|---|---|---|---|---|---|
| Project ID| --projectId|-j|String|null| GCP Project Name|
