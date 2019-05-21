# GCP Overview

For the following examples we will assume that the Furnace Instance has been called `furninst` and that the stack being deployed is called `furnstak`.

## Ignite Resources

The following resources are created by the bootstrap process when running `furnace ignite`:

- Storage Buckets
  - `furninst-{region}-furnace-secrets-{uid}` used to store encrypted secrets
  - `furninst-{region}-furnace-artifacts-{uid}` stores the stack functions/modules when a stack is deployed
  - `furninst-{region}-furnace-bootstrap-{uid}` stores the `deployExec` and `deployTrigger` function templates
- Key Ring
  - Used to generate and store encryption keys `{project name}-{instance name}-key-ring-{uid}`
- Keys
  - Secrets Key, used to encrypt the secrets stored in the secrets bucket `{project name}-{instance name}-secrets-key-{uid}`
  - State Key, used to encrypt the state file `{project name}-{instance name}-state-key-{uid}`
- Cloud Function (`furninst-deploy-trigger`)
  - Used to process the webhook messages sent from Github 
  - Uses an HTTP trigger
  - Sends output to a Topic `furninst-deploy`
- Topic (`furninst-deploy`)
  - Recieves messages from the `furninst-deploy-trigger` function
- Subscription (`gcf-{stack name}-deploy-exec-{stack name}-deploy`)
  - Used to receive messages from the deploy trigger Topic
- Cloud Function (`furninst-deploy-exec`)
  - Triggered by messages on the `furninst-deploy` Topic
  - Creates a zip file of the stack repository and stores it in the artifacts bucket
  - Starts the Cloud Build process to deploy the stack
- Cloud Build
  - Loads the deploy container
  - Grabs the stack zip from the artifacts bucket and builds the stack 
  
## Deployment Resources

Deployment resources will be dependant upon how you craft your stack, but typically the following will be deployed:

- Sources
  - Deploys a Pub/Sub Topic with the naming convention {stack name}-{source name}-{environment} so if we deployed our example stack to `dev` with a source named `source1` our Kinesis Stream would be called `furnstak-source1-dev`
- Taps
  - Taps will be backed by a function/module, naming convention is {stack name}-{source name}-{environment} so if our tap was called `tap1` we would get `furnstak-tap1-dev`
  - A tap will create an output Pub/Sub Topic as per the tap but with `out` appended to it ie. `furnstak-tap1-dev-out`
- Pipelines
  - Pipelines will follow the same naming convention as Taps for both the Cloud Function and output Pub/Sub Topic.
 

## Log Locations

Logs in GCP can be found in the Logs Viewer [https://console.cloud.google.com/logs/viewer](https://console.cloud.google.com/logs/viewer) or on the logs tab for each resource.
For deployments you will generally want to check Cloud Build -> History for the latest logs [https://console.cloud.google.com/cloud-build/builds](https://console.cloud.google.com/cloud-build/builds) 