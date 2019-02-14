# Writing your first Furnace module

## Writing your first Furnace module

### What is Furnace

Furnace is an Open Source project aimed at providing a framework to make event processing painless while taking advantage of serverless to provide great cost savings and scalability. If you want to read more about why we built Furnace, what advantages you or your company can get from it and how the journey has led us here, please take 5 minutes to read: [_Introducing Furnace_](https://medium.com/@dannywaite7/introducing-furnace-e4d7b1b51932)

### TLDR - If you really don't feel like reading the whole explanation coming

If you already know about Furnace and what modules are and just need to have a quick overview on how to build them, this section is for you! Only 4 steps:

1. Clone the [module template repo](https://github.com/ProjectFurnace/module-templates/tree/master/nodejs8.10)
2. Modify config.yaml and module.yaml to suit your module specifics. Those files should almost be self-explanatory but if not, just check the modules' documentation [here](../basics/system-architecture/modules.md)
3. Modify src/index.js so handler does what you expect your module to do. The handler function receives the event data already as a JSON object and expects the modified event as return.
4. Include the module in your pipeline and you are good to go!

### How do modules work

It is actually pretty simple! Modules are connected to an input queue from where they read events. Then some magic is done with the event data \(up to the module builder\), and finally the event is sent to an output queue so it can be picked by the next module in the pipeline.

In the specific case of AWS, the modules execute as Lambda functions that read data from a Kinesis stream and _usually_ output data to another Kinesis stream. I say usually because there may be cases where the data is just sinked to an external data storage and then it does not need to be sent to an output Kinesis stream.

### Types of modules

We have defined 5 types of modules that Furnace works with. New ones may be included in the future while some of the ones I will describe are still on a planning/architectural phase. Let's get more detail into each of them:

* _ingest:_ those take care of ingesting data and normalising it **\(further on this point on the next section\)**.
* _enrich:_ used to enrich data. Think for example about adding Geo information to IP events
* _filter:_ as the name suggests, those filter events based on specific criteria. There is still work to do on the definition of those.
* _action:_ intended as a way to action based on certain event matches. The current architecture for actions is being defined and any suggestions are more than welcome. Join us on our [Slack](https://projectfurnace.slack.com) channel to discuss it!
* _sink:_ these modules take events from the system and send them to some external system or data storage/lake \(like Elasticsearch\).

### Normalised event format

One of the things we realised when working with other event processing platforms was the pain of having to deal with events that were completely different depending on where they were coming from and which platform they were being processed by. As a result, we decided that Furnace would provide a standardised event format, so any events going into modules that had to do any enrichment, or data processing, would work no matter where the data was coming from, without modifications. To allow that, when data is fed into Furnace, it needs to be mapped to the normalised event format that Furnace is using. We will explore more in depth the creation of ingest modules and the necessary field mapping in upcoming blog posts.

### Module structure

To get started, the easiest way is to use the Furnace CLI. You will need it also to create a Furnace stack, so in case you do not have it installed, check the Furnace [_Getting Started_](../basics/getting-started.md) section of the readme for instructions on how to do so.

Once you have the CLI installed, you can just type:

```bash
furnace module new node8.10 <name>
```

This will create a folder with some files in it that define specifics about the module and that we will talk about later on.

Another option you have to get started is to clone the module template available in Github, here:

[https://github.com/ProjectFurnace/module-templates/tree/master/nodejs8.10](https://github.com/ProjectFurnace/module-templates/tree/master/nodejs8.10)

As you can guess from that link, only NodeJS is available right now as a language to develop with Furnace, but we are working hard to include other languages like Python, Go and potentially others.

Now, digging further into the files on the module:

#### module.yaml

This file has some general information about the module itself, such as:

* id
* version
* runtime
* category

Those are really the main fields, but as you can see in the template, it is fairly easy to figure out what to include in that file and if you need additional information, you can always head to the [full documentation about modules](../basics/system-architecture/modules.md). If you have used the CLI, some fields will have already been filled out for you with the expected values \(mainly id and runtime\), but if you are just cloning from Git, you will need to change those manually.

#### config.yaml

This file holds the configuration parameters that your module accepts. If there are no parameters to be setup for the module, you may as well leave this file blank.

As with the module.yaml file, you can head over to the [modules full documentation](../basics/system-architecture/modules.md) for additional information. In here you will be specifying also the type of data a specific parameter accepts, whether or not it is mandatory, defaults and those sorts of things.

#### src/index.js

This is where your code is actually going to reside.

When we built Furnace, we did put a lot of effort into making sure that you had to write as little code as possible to make things happen and to help you avoid any boilerplate code. It's the same concept of serverless \(which Furnace is built on\) but with an additional iteration to take care of connecting modules between themselves transparently for you.

So, what do you need to do to have your module do something useful? Well, you essentially just need to modify your event in the way that you find relevant inside your handle function and return it. That's it! It's that simple. Let's see an example.

Imagine you have events coming into Furnace that include a MAC address of the device that generated the event. Let's see how we can enrich those events to include the Manufacturer of the device in the actual event.

Also remember that we are using a normalised event format, so the source mac address is always in the same field, which is going to be: _network.src\_mac_

As this is just an example, we are not going to work with a full database of vendors, but with one. Expanding the code to use a full vendor DB is interesting, but if you have done some coding before, you'll know how to do that easily and it does not matter if it's Furnace or any other platform, you will do it the same way.

We are going to enrich the data just in the case of one specific Apple MAC address match: _f0766f_

```javascript
function handler(event) {
  // first we want to make sure we have a src_mac field in the event
  if (event.network.src_mac) {
    // then we want to normalise the mac address by removing colons, dashes and lowercasing it
    mac = event.network.src_mac.replace(/:/, '').replace(/-/, '').toLowerCase();
    // finally, if we have a match, we will add the information to the event
    if ( mac.substring(0,6) === 'f0766f' ) {
      event.producer.vendor = 'Apple'
    }
  }
  return event;
}

module.exports.handler = handler;
```

It is fairly simple as you can see. If you check the comments, most of the code is just to make sure we have the necessary fields to enrich our data as well as to guarantee the event information is sanitised to always match our check.

So with that, you would just need to export the handler as shown in the last line, make sure you have the modules.yaml and config.yaml files ready and off you go.

You can also, head over to our modules-core repo to check the code of some of the modules we have developed for you to test Furnace. Those will give you ideas on how different things can be achieved and hopefully help clear any detail that might have not been properly explained in the previous lines.

We are excited to see what you can make with Furnace and we cannot wait to hear about new modules you are planning to create or ways you think Furnace can be used for the projects you are involved in. Please, please, join us on our [Slack](https://projectfurnace.slack.com) and also send any issues or suggestions there or in Github.

In the upcoming blog posts I will provide some guidelines and samples on creating tests for modules, so stay tuned!

