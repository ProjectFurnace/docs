# Azure Overview

For the following examples we will assume that the Furnace Instance has been called `furninst` and that the stack being deployed is called `furnstak` and we are deploying the `dev` environment.

## Ignite Resources

- Resource Group (`{stack name}rg` ie. `furninstrg`)
  - Used to group all the Furnace Instance resources
- Storage
  - `{stack name}artifacts` ie `furninstartifacts` used to store the module/function code
  - `{stack name}bootstrap` ie `furninstbootstrap` used to store the deploy code
- App Service (`{resource group name}-deploy` ie `furninstrg-deploy`)
  - Contains the functions for deploy
    - `deploy-trigger` Accepts webhook request from Github and puts a message into the deploy EventHub queue
    - `deploy-exec` Triggered by events on the deploy EventHub queue and launches the deploy container.
  - App Insights
- Event Hubs Namespace (`{resource group name}-deployHubNS` ie `furninstrg-deployHubNS`)
  - Contains the Event Hub Instance `{resource group name}-deployhub` to receive messages from the `deploy-trigger` function 
- Key Vault (`{resource group name}-vault` ie `furninstrg-vault`)
  - Used for storing secrets 
- User Assigned Identity (`FurnaceDeployUserIdentity`)

## Deployment Resources

- Resource Group (`{stack name}-RG-{environment}` ie `furnstak-RG-dev`)
  - Contains the stack resources
- Event Hubs Namespace ({stack name}-hubns-{environment} ie `furnstak-hubns-dev`)
  - Contains the Event Hub Instances for the queues between functions
  - Source Event Hub will be named `{stack name}-{source name}-{environment}` ie for a source name of `source1` it would be `furnstak-source1-dev`
  - Function events will have output Event Hubs created as `{stack name}-{function name}-out-{environment}` so if our function was called `function1` it would be `furnstak-function1-out-dev`
- Container Group
  - Contains the container for deploying the stack 
- Sink resources as described in the stack 

## Log Locations

Logs for deployment can be found in the Container Group resource in the stack Resource Group.

## Known Issues

For some reason you may get a 500 error on the deploy function trigger, if this happens try restarting the deploy function app. 