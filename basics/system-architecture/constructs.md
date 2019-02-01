# Constructs

**Source** - Defines the source of data, usually this is stream from Apache Kafka or AWS Kinesis but can be anything you define. We provide some standard sources out of the box.

**Tap** - Taps connect to a source. Their job is to parse and normalise data into a common format. A default set of Taps are provided and you can create new Taps by creating a module which maps to a serverless function.

**Pipeline** - Pipelines create a linear path for data to flow through a set of operations. Pipelines are connected to Taps and a Tap can feed multiple Pipelines.

**Sink** - A Sink is where your data arrives after it exits a Pipeline. A Sink could be a data lake or object storage. Multiple Pipelines can feed into a Sink and a Pipeline can feed into multiple Sinks.

**Resource** - Resources are used to initiate resources native to the environment in which Furnace is being deployed such as data stores \(eg. Redshift on AWS\)

**Actions** - Once data has been processed by your Pipelines, Actions make use of the structured data react and automate tasks in real-time.

**Stack** - A Stack is comprised of one or more end to end data flows into a logical container. A Stack can have multiple Environments \(Dev, Staging, Production\).

