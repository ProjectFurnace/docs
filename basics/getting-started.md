# Getting started

Our aim is to have you up and running in less than 5 minutes. You'll need to meet the following prerequisites \(see current limitations\):

* Node 8.x LTS and NPM 6.x \([https://nodejs.org](https://nodejs.org/)\)
* AWS CLI and a configured profile \([https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\)
* A GitHub account and Personal Access Token \([https://github.com/settings/tokens](https://github.com/settings/tokens)\)

The process starts with installing the Furnace CLI and running `furnace ignite`

```text
npm install @project-furnace/furnace-cli -g
furnace ignite
```

You'll be asked a series of questions relating to your environment. This will take a few minutes to complete. On AWS a CloudFormation template is being deployed that contains and ECS Cluster to execute the Deployment Service when needed, a series of Lambda functions to react to various events and a Simple Queue Service \(SQS\) to route messages between components.

Once this command has completed you're ready to create a new stack.

```text
furnace new my-stack
cd my-stack
```

Again you'll be prompted to answer a series of questions about your Stack and will attempt to initialise various of the required parts including GitHub repositories and webhooks. You'll now have a new repository created that you can start to build on. You can learn more about working with Furnace Stacks with the 5 Minute Walkthrough.

The `stack.yaml` contains a list of environments, by default you have `dev`, `staging`, `production`. Once you're ready deploy your first stack, we do so using a standard `git` flow.

```text
git commit -am "initial commit"
git push origin master
```

This pushes to the first environment defined in your Stack, you can see the deployment status in `environments` in the Stack repository in GitHub or using the `furnace status` command inside the Stack directory.

Once you're happy that your Stack is functioning how you expected in your initial environment you can `promote` it to an upstream environment eventually towards its final/production environment. For example, keeping with the default environments, to promote the `dev` environment to `staging` you would simply:

```text
furnace promote dev
```

