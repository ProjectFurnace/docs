# Furnace modules

This is a base document to define how modules for Furnace should be built and packaged to be compatible with the platform. This definition is currently a draft so it may change in the future.

## Foundation

One of the main focus of the Furnace project is to make Dev/Sec/Ops life easier. As such, the modules should be built based on a series of premises that should always be kept in mind:
- A basic parameters file has to include any relevant details about the module
- The compression has to be based on some widely-adopted format
- Folder structure should be simple, specific and self explanatory
 
 ## Folder structure

<pre>
 ├── package.yml
 ├── config.yml
 └── module/
     ├── index.js
     └── ...
</pre>

As can be seen, it is a really simple folder structure, with a *package.yml* and a *config.yml* files on the root and a *module* folder with  all the code for the module, which can include subfolders.

## Compression

The chosen compression format is ZIP, with the mentioned folder structure directly at the root level of the ZIP file. Any compression level can be used, but given the fact that the file will only be uncompressed once, using the maximum possible level is suggested. No encryption should be used in the ZIP file.

## Package verification

An MD5 file named *package.md5* should be provided along with the *package.zip* file containing the MD5 sum of the *'*package.zip*'* file.

## Package description file: *package.yml*

The package description file (*package.yml*) contains basic information about the module, dependencies and its authors.

It is structured as a yaml file. The minimal *package.yml* file looks like this:

<pre>
id: my-package
version: 1.0.0
category: ingest
core-version:
  min: 0.1.0
  max: 0.3.1
meta:
  name: My Package
  description: This is the description for the package My Package
  keywords: [my-package, furnace]
  website: https://projectfurnace.github.io
author:
  name: Project Furnace
license: MIT
</pre>

Whereas a more extended version with additional information would look like this:

<pre>
id: my-package
version: 1.0.0
category: ingest
core-version:
  min: 0.1.0
  max: 0.3.1
meta:
  name: My Package
  description: This is the description for the package My Package
  keywords: [my-package, furnace]
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
  - my-other-package: >=1.0.1
</pre>

### *package.yaml* parameter details

- **id** (Mandatory, 64 chars, [a-z-_]): A package identifier. It will be used as the ZIP name.
- **version** (Mandatory, 8 chars, [0-9.]): The package version. Should always be incremental and in the format XX.YY.ZZ
- **category** (Mandatory, Fixed list of options): What category this package belongs to. Options are: *ingest, enrich, filter, action and sink*.
- **core-version** (Mandatory): Used to specify what is the minimum and maximum version of the core this package will work with.
  - **min**: The oldest version of Furnace core the package will work with.
  - **max**: The newest version of Furnace core the package will work with.
- **meta**
  - **name** (Mandatory): The package name that will show both on the UI and in the marketplace.
  - **description** (Mandatory): Description for the package to be shown in the marketplace.
  - **keywords**: Keywords to associate with the package. Those will be used in the marketplace.
  - **website**: Website associated with the package. This will be used in the marketplace.
- **author**
  - **name** (Mandatory): Package author name.
  - **website**: Author website.
  - **email**: Author email address.
- **contributors**
  - **name**: Contributor name.  
    **website**: Contributor website.  
    **email**: Contributor email address.
- **license**: License for the package
- **dependencies**: A list of other Furnace packages this specific package depends on. Format to specify them is '- package-id: version' where version is the version number preceded by:
  -  \> (higher)
  -  \>= (higher or equal)
  -  < (lower)
  -  <= (lower or equal)
  -  just the version number itself for an exact match

## Configuration parameters file: *config.yml*

The configuration parameters file (*config.yml*) describes all the configuration parameters the module accepts.

It is structured as a yaml file. If a module has no configurable parameters the file can be empty or missing.

<pre>
id: my-package
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

### *config.yml* parameter details

- **id** (Mandatory, 64 chars, [a-z-_]): A package identifier. It must match the one in package.yml.
- **version** (Mandatory, 8 chars, [0-9.]): The package version. It must match the one in package.yml.
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