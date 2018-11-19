# Blueprints

Blueprints allow for a whole system flow to be imported or exported. Those are defined as a set of YAML files that can be packaged together. The same way a house blueprint provides details in how to build it, Furnace blueprints provide a means to create definitions on how to build a Furnace flow in a way that is programatically easy to import.

## Files

Blueprints can bundle the following files:

<pre>
ingests.yaml
enrichments.yaml
sinks.yaml
actions.yaml
settings.yaml
</pre>

A minimal blueprint requires at least:
- **ingests.yaml**: if we do not ingest data, the system has no use.
- **enrichments.yaml**: at least an enrichment pipeline needs to be defined, even if it just passes data through so it can reach a sink or action queue.

## File details

### ingests.yaml

Contains information on the modules used in the ingestion stage as well as any relevant configuration parameters for those modules.

<pre>
- name: test-module
  version: 1.2.0
  enabled: true
  shards: 2
  config:
    param-id: val

- name: test-module2
  version: 1.2.0
  enabled: true
  shards: 2
  config:
    param-id: val
</pre>

- **module-id** (mandatory): the module identifier
  - **version** (mandatory): the module version
  - **enabled** (defaults to *false*): whether this module is enabled or not
  - **shards** (defaults to 1): how many shards to use for the input queue
  - **config**
    - **param-id**: key-value pairs with configuration parameters for this module

<br/>

### enrichments.yaml

The *enrichments.yaml* file contains the description of one or more pipelines for enrichment. Those pipelines are comprised of one or more modules with their configuration parameters.

<pre>
- name: pipeline-name
  modules:
    - id: a-module
      version: 1.2.1
      workers: 2
      config:
        param-id: a
    - id: another-module
      version: 1.5.0
      workers: 1
      config:
        param-id: c

- name: another-pipeline-name
  modules:
    - id: third-module
      version: 1.4.2
      workers: 2
      config:
        param-id: a
        param-id-b: b
    - id: a-module
      version: 0.4.2
      workers: 1
</pre>

- **pipeline-name** (mandatory): the pipeline name
  - **modules**
    - **id** (mandatory): identifier for the module
    - **version** (mandatory): the module version
    - **workers** (defaults to 1): number of workers for this module
    - **config**
      - **param-id**: key-value pairs with configuration parameters for this module

<br/>

### sinks.yaml

<pre>
- name: module-id
  version: 1.3.7
  enabled: true
  config:
    param-id: val
</pre>

- **module-id** (mandatory): the sink module identifier
  - **version** (mandatory): the module version
  - **enabled** (defaults to *false*): whether this sink is enabled or not
  - **config**
    - **param-id**: key-value pairs with configuration parameters for this sink

<br/>

### actions.yaml

<pre>
# pending definition
</pre>

### settings.yaml

The *settings.yaml* file includes some general configuration parameters for the system. This file should only be provided with backup configurations of previous deployments, as running deployments will already have the values defined in here setup.

<pre>
platform:
  type: aws
  parameters:
    vpcName: furnaceVpc
    vpcCreateIfRequired: true
    numberOfAvailabilityZones: 1

shards:
  enrichInputQueue: 1
  actionInputQueue: 1
  sinkInputQueue: 1
</pre>

- **platform**
  - **type** (mandatory, options: aws): the platform type
  - **parameters**
    - **param-id**: key-value pairs with configuration parameters for this specific platform
- **shards**
  - **enrichInputQueue** (defaults to 1): number of shards on the enrichment input queue.
  - **actionInputQueue** (defaults to 1): number of shards on the action input queue.
  - **sinkInputQueue** (defaults to 1): number of shards on the sink input queue.
