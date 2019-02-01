# Components

To provide these capabilities Furnace assembles a number of components into an autonomous system.

**Furnace CLI** - Responsible for 'Igniting' Furnace, creating new stacks and many helpful commands to keep you productive.

**Bootstrap Template** - Provisions the minimal components you need to create an instance of Furnace. For AWS, this is a CloudFormation Template.

**Stack Template** - Provides a starting point for your stack with a set of configuration files, we provide a starter template and you can create your own.

**Module Template** - Boilerplate used to create your own modules.

**Function Template** - Wraps around a module for various platforms and languages.

**Module Repository** - Contains a library of prebuilt modules that can be added to a Stack. We provide a set of 'core' modules and you can create your own repository.

**Stack Repository** - The various configuration files and modules for a specific Stack.

**State Repository** - Since everything is stored in Git, each stack requires a State Repository to store current state of a Stack and Environment.

**Deployment Service** - Responsible for receiving deployment events \(via `git push` or `furnace promote`\), building artifacts and provisioning the associated infrastructure on the chosen platform.

**Artifact Repository** - Stores module artifacts together with hashes and versions them.

**Log Repository** - Contains various logs files created as part of the Deployment process.

