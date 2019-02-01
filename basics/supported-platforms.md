# Limitations

As this is currently a community preview, there are some limitations to be aware of.

**AWS** - Since a key goal is for Furnace to run natively on cloud providers, the initial release targets AWS exclusively since it has the largest user base. More platforms to follow shortly including Kubernetes for on-premise deployments.

**GitHub** - Furnace aims to provide an end-to-end development and deployment flow therefore tight integration with Git providers is necessary, we initially support GitHub but GitLab and regular Git repository support will quickly follow.

**Node 8.10**- Another key goal of Furnace its to allow developers to write in any language they wish. In this initial release we provide helpers and wrappers for Node 8.10 which enables you to write modules that are not tied to a specific platform. We'll be creating support for a wide range of languages in the coming weeks.

**ECS** - Furnace uses a container to perform the deployment of your stacks to the underlying infrastructure since running this in a serverless function is impractical for several reasons. Currently we're spinning up a single node Elastic Container Service as part of the bootstrap process. The preferred approach will be to use Fargate for this however it is not currently supported in many regions and thus we took the decision to use ECS until further support is reached.

**GitHub Auth/HTTPS** - Currently only HTTPS based repositories are supported together with Personal Access Tokens however we'll look to support other forms of integration in the coming weeks.

