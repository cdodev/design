- [A purely functional stack for building apps](#sec-1)
- [Benefits](#sec-2)
  - [Granularity](#sec-2-1)
  - [ACID](#sec-2-2)
  - [Simple model for creating state](#sec-2-3)
  - [Multi use events](#sec-2-4)
  - [Out of the box integrations](#sec-2-5)
- [What the framework will do](#sec-3)
  - [Commands](#sec-3-1)
    - [REST](#sec-3-1-1)
  - [Event stream](#sec-3-2)
    - [Apache Kafka](#sec-3-2-1)
    - [Web socket](#sec-3-2-2)
    - [Kibana stream](#sec-3-2-3)
- [What the client programmer will do](#sec-4)
  - [Business logic](#sec-4-1)
    - [Database schema](#sec-4-1-1)
    - [Command interpreter](#sec-4-1-2)
    - [Event processor](#sec-4-1-3)
    - [](#sec-4-1-4)

# A purely functional stack for building apps<a id="orgheadline1"></a>

The main difference of a functional app is that we don't mutate state directly.
In fact current state is derived entirely from events that have been observed
to happen, so we should be able to recover any state by replaying the events we
have seen. The stream of events that has occurred is the golden source of data
in the system and can be folded down into any other data source to express state.

# Benefits<a id="orgheadline8"></a>

In this section I'll cover what we want from a "purely functional" applications
stack. At first appearances it can seem a lot of trouble so we should motivate
all this with a look at the benefits.

## Granularity<a id="orgheadline2"></a>

Because we store what's happening at the level of granularity of events in the
app that have semantic meaning, we never loose that granularity. This is in
contrast to apps with a traditional CRUD back end where SQL commands are issued
directly. In these apps you only see things as they are right now - you loose
the flow of how things have got to be this way. If you're lucky in the old model
you might have a "last updated" column or similar. You might also have bland
data-centric event data in a WAL log or similar so that you can see what changes
have happened in the database over time.

## ACID<a id="orgheadline4"></a>

At the point of creation events will have access to a relational database and
will therefore be able to use all of the referential integrity tools to ensure
that the events being generated are valid. Let's say for example that you're
building a app that tracks to-dos as they relate to wider goals. If your
business logic states that a to-do must have a related goal you might have a
`todo` table and a `goal` table with a foreign key constrains between them in
your `CreateToDo` [command](#orgheadline3) can simply attempt to insert into the `todo` table. If
the inserts succeeds then the event is produced. If the foreign key (or any
other constraint) is violated it won't be.

## Simple model for creating state<a id="orgheadline5"></a>

Because we have all the information required to re-create state is in the events
the conceptual model is simply a left fold:

```haskell
foldlM :: (Foldable t, Monad m) => (state -> event -> m state) -> state -> t event -> m state
```

This should be pretty much all the client programmer has to provide. This also
means that to make a representation of the data available in different systems
as needed you only have to refold a source of events that you know to be valid.
For example you might want to replicate the data to elastic for searching or
some kind of data warehouse for analysis.

## Multi use events<a id="orgheadline6"></a>

As alluded to in the last section, once you have a stream of valid events and a
simple model for how to react to them the world opens up. It becomes very simple
to write small apps at the edges of your systems that react to changes in the
world in real time. In our experience even the UI can be built in this way.

## Out of the box integrations<a id="orgheadline7"></a>

Several ways of looking at event data are so useful that they will be provided
out of the box. These include streaming events into elastic so that they can be
used in kibana alongside what you might generate from system logs. We might also
think about a bridge into something like snowplow for analytics. 

# What the framework will do<a id="orgheadline14"></a>

The framework we will provide will take care of 

## Commands<a id="orgheadline3"></a>

### REST<a id="orgheadline9"></a>

## Event stream<a id="orgheadline13"></a>

### Apache Kafka<a id="orgheadline10"></a>

### Web socket<a id="orgheadline11"></a>

### Kibana stream<a id="orgheadline12"></a>

# What the client programmer will do<a id="orgheadline20"></a>

## Business logic<a id="orgheadline19"></a>

### Database schema<a id="orgheadline15"></a>

### Command interpreter<a id="orgheadline16"></a>

### Event processor<a id="orgheadline17"></a>

### <a id="orgheadline18"></a>
