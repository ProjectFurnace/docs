# AWS Overview

For the following examples we will assume that the Furnace Instance has been called `furninst` and that the stack being deployed is called `furnstak`.

## Ignite Resources

The following resources are created by the bootstrap process when running `furnace ignite`:
- S3 Buckets
  - `furninst-{region}-furnace-artifacts-{uid}` stores the stack functions/modules when a stack is deployed
  - `furninst-{region}-furnace-bootstrap-{uid}` stores the `deployExec` and `deployTrigger` function templates
- API Gateway (`Deploy API`)
  - Used as an endpoint by the GitHub webhook
- Lambda Function (`furninst-deployTrigger`)
  - Used to process the messages sent to the Deploy API
  - Uses an API Gateway trigger
  - Sends output to an SNS Queue `furninst-FurnaceStackUpdates`
- SNS Queue (`furninst-FurnaceStackUpdates`)
  - Recieves messages from the `furninst-deployTrigger` lambda
  - Invokes lambda function `furninst-deployExec` to deploy stacks
- Lambda Function (`furninst-deployExec`)
  - Triggered by messages on the `furninst-FurnaceStackUpdates` SNS Queue
  - Starts the deployment ECS container `stackUpdater`
- ECS Cluster and Task Definition
  - Task Definition `furninst-stackUpdaterTask` stores the definition for the container which handles the deployment of stacks `stackUpdater`
  - Invoked by the `furninst-deployExec` Lambda
  - Pulls a container from Docker Hub `projectfurnace/deploy`
  - Container source code can be found here [https://github.com/ProjectFurnace/deploy](https://github.com/ProjectFurnace/deploy)
  - *Note: at present we are using a single machine ECS Cluster however we will be moving to Fargate as soon as it's supported in all regions*
- AWS Secrets (Various)
  - `furninst/apiKeySecret` Used to secure the API Gateway
  - `furninst/GitHookSecret` Used to secure the Github Webhook
  - `furninst/GitToken` Used to update Github with status information
- Networking resources
  - Furnace will create it's own VPC and associated resources to handle deployments within it's own network
- IAM
  - Furnace will create various IAM roles, groups and policies to manage deployments 
  
**Most of these resources are deployed by a CloudFormation template which is called the same name as your Furnace instance `furninst` in this case**


## Deployment Resources

Deployment resources will be dependant upon how you craft your stack, but typically the following will be deployed:
- Sources
  - Usually a Kinesis Stream, naming convention is {stack name}-{source name}-{environment} so if we deployed our example stack to `dev` with a source named `source1` our Kinesis Stream would be called `furnstak-source1-dev`
- Taps
  - Taps will be backed by a function/module, naming convention is {stack name}-{source name}-{environment} so if our tap was called `tap1` we would get `furnstak-tap1-dev`
  - A tap will create an output Kinesis Stream or SQS named as per the tap but with `out` appended to it ie. `furnstak-tap1-dev-out`
- Pipelines
  - Pipelines will follow the same naming convention as Taps for both the Lambda and output Kinesis Stream or SQS Queue.
- Sinks
  - Sinks are typically an S3 Bucket, Kinesis Stream or Kinesis Firehose depending on how you've configured it. 

## Log Locations

All AWS logs are stored in CloudWatch.  A log group will be created for each Lambda used by both your Furnace Instance and your Stacks. 
Typically you will want to check the ECS Stack Updater Task log for tracking down issues during a deployment.  This log is found under the log group `/ecs/{stack name}-stackUpdaterTask`

Log groups for Lambdas are only created when the lambda is first invoked.  If you are not seeing a log group for your Lambda check to make sure it's being triggered. 
