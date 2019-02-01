# Furnace

![](https://github.com/ProjectFurnace/furnace/raw/master/assets/furnace-logo-horiz-black.png)

### What is Furnace?

A streaming data processing framework built on Serverless \(or FaaS\) and GitOps that allows you to build complex pipelines rapidly.

> This is currently a community release, we're looking for feedback and there are currently a number of limitations.

Furnace aims to simplify the creation and deployment of streaming data applications for things like Security Information and Event Management, Internet of Things, Clickstream Analysis and anywhere you need to process larges amounts of data and:

* Parse, cleanse, normalise before further processing
* Enrich, filter, calculate by assembling pipelines
* Store in a Data Lake for querying and performing analysis
* Create action workflows to respond to events in your data

### Serverless

We do this in an abstracted way that isn't attached to any particular cloud or platform however the key being that we try to run **natively** on the chosen underlying platform. That means, when running on AWS we make use of its Serverless resources such as Lambda, Kinesis and a Data Lakes such as Redshift, S3 and Elasticsearch. This approach allows Furnace to provide numerous benefits that we believe make it a compelling framework for applications of this kind:

* Dramatic cost savings, only pay for the processing power you require
* No servers or clusters to manage, also saving time and money
* Scale upwards and back to zero if necessary as your traffic flow varies
* Avoid cloud/vendor lock in

### DevOps Lite

Furnace is an opinionated but open framework using best practices in terms of efficiency and security that enables teams to focus on building upon the intelligence they have in their data with the least amount of pain possible. Simply commit configuration and code to git and Furnace will build and deploy into multiple environments \(eg. Dev, Staging, Production\). We've provided tooling that gets out of your way allowing you to be productive using the tools and languages you're used to.

