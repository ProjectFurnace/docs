# Furnace modules

This is a base document to define how modules for Furnace should be built and packaged to be compatible with the platform. This definition is currently a draft so it may change in the future.

## Foundation

One of the main focus of the Furnace project is to make Dev/Sec/Ops life easier. As such, the modules should be built based on a series of premises that should always be kept in mind:
- A basic parameters file has to include any relevant details about the module
- The compression has to be based on some widely-adopted format
- Folder structure should be simple, specific and self explanatory
 
 ## Folder structure

<pre>
 ├── info.yaml
 ├── config.yaml
 └── module/
     ├── index.js
     └── ...
</pre>

As can be seen, it is a really simple folder structure, with an *info.yaml* and a *config.yaml* files on the root and a *module* folder with  all the code for the module, which can include subfolders.

## Compression

The chosen compression format is ZIP, with the mentioned folder structure directly at the root level of the ZIP file. Any compression level can be used, but given the fact that the file will only be uncompressed once, using the maximum possible level is suggested. No encryption should be used in the ZIP file.

## Module verification

An MD5 file named *module.md5* should be provided along with the *module.zip* file containing the MD5 sum of the *module.zip* file.

## Module description file: *info.yaml*

The module description file (*info.yaml*) contains basic information about the module, dependencies and its authors.

It is structured as a yaml file. The minimal *info.yaml* file looks like this:

<pre>
id: my-module
version: 1.0.0
category: ingest
runtime: nodejs8.10
core-version:
  min: 0.1.0
  max: 0.3.1
meta:
  name: My Module
  description: This is the description for the module My Module
  keywords: [my-module, furnace]
  website: https://projectfurnace.github.io
author:
  name: Project Furnace
license: MIT
</pre>

Whereas a more extended version with additional information would look like this:

<pre>
id: my-module
version: 1.0.0
category: ingest
runtime: nodejs8.10
core-version:
  min: 0.1.0
  max: 0.3.1
meta:
  name: My Module
  description: This is the description for the module My Module
  keywords: [my-module, furnace]
  website: https://projectfurnace.github.io
author:
  name: Project Furnace
  website: https://twitter.com/projectfurnace
  email: hello@projectfurnace.io
contributors:
  - name: A Contributor
    website: https://twitter.com/acontributor
    email: iam@acontributor.com
  - name: D Contributor
license: MIT
dependencies:
  - my-other-module: >=1.0.1
</pre>

### *info.yaml* parameter details

- **id** (Mandatory, 64 chars, [a-z-_]): A module identifier. It will be used as the ZIP name.
- **version** (Mandatory, 8 chars, [0-9.]): The module version. Should always be incremental and in the format XX.YY.ZZ
- **category** (Mandatory, Fixed list of options): What category this module belongs to. Options are: *ingest, enrich, filter, action and sink*.
- **runtime** (Mandatory, Fixed list of options): What runtime to be used to run this module. Options are: *nodejs6.10, nodejs8.10, python2.7, python3.6, go1.x*.
- **core-version** (Mandatory): Used to specify what is the minimum and maximum version of the core this module will work with.
  - **min**: The oldest version of Furnace core the module will work with.
  - **max**: The newest version of Furnace core the module will work with.
- **meta**
  - **name** (Mandatory): The module name that will show both on the UI and in the marketplace.
  - **description** (Mandatory): Description for the module to be shown in the marketplace.
  - **keywords**: Keywords to associate with the module. Those will be used in the marketplace.
  - **website**: Website associated with the module. This will be used in the marketplace.
- **author**
  - **name** (Mandatory): Module author name.
  - **website**: Author website.
  - **email**: Author email address.
- **contributors**
  - **name**: Contributor name.  
    **website**: Contributor website.  
    **email**: Contributor email address.
- **license**: License for the module
- **dependencies**: A list of other Furnace modules this specific module depends on. Format to specify them is '- module-id: version' where version is the version number preceded by:
  -  \> (higher)
  -  \>= (higher or equal)
  -  < (lower)
  -  <= (lower or equal)
  -  just the version number itself for an exact match

## Configuration parameters file: *config.yaml*

The configuration parameters file (*config.yaml*) describes all the configuration parameters the module accepts.

It is structured as a yaml file. If a module has no configurable parameters the file can be empty or missing.

<pre>
id: my-module
version: 1.0.0
config-groups:
  config-group-name:
    param-id:
      label: Enrich on field
      mandatory: true
      type: single
      default: network.ipv4_src
      visible: true
      validator: '[A-z\._]+'
      optionset:
        - network.ipv4_src: Source IP
        - network.ipv4_dst: Destination IP
      help: Specify the field for which to do the Geo IP lookup
  other-config-group:
    other-param-id:
      label: Tag with
      mandatory: true
      type: text
      default: siem
      visible: true
      validator: '[A-z]+'
</pre>

### *config.yaml* parameter details

- **id** (Mandatory, 64 chars, [a-z-_]): A module identifier. It must match the one in *info.yaml*.
- **version** (Mandatory, 8 chars, [0-9.]): The module version. It must match the one in *info.yaml*.
- **config-groups**: This groups the different config blocks.
  - **config-group-name**: The name of this specific config group as shown in the UI.
    - **param-id**: An identifier for the configuration parameter. Will need to match the var name in code.
      - **label**: The label to use in the UI.
      - **mandatory**: Whether it is mandatory or not to fill this field.
      - **type**: What type of field is this. Options are: *text, single, multi*.
      - **default**: The default value for the field.
      - **visible**: Whether or not this config parameter is visible in the UI.
      - **validator**: A valid regex expression to be used to validate the value provided as configuration parameter.
      - **optionset**: List of valid values for the field if those are only a few. Described as a key-value array with the value as the key and the label as the value.
      - **help**: Tips for help in the UI.