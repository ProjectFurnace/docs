# System architecture

Furnace is essentially a framework to make it really simple to build event processing pipelines. To be able to construct such a system, you need a few different things. Thinking left to right, input to output:

- **Inputs**: to be able to ingest data into it. Everything else wouldn't be very useful otherwise. We think plumbing provides a great analogy to many of the terms an event processing system uses, so in Furnace, on the input side we have **sources** and **taps**.
- **Processing bits**: to act on the ingested data and either enrich it, modify it or do any sort of work with it. We call them **functions**
- **Outputs**: to send that data somewhere else, whether it is a database, cold storage or another system. Following the plumbing analogy, we have called them **sinks**.

With those three sections you have the basic building blocks ready and you just need to piece them together and be sure that it's easy to do that.
