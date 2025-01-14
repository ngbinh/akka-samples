This tutorial contains a sample illustrating an CQRS design with [Akka Cluster Sharding](https://doc.akka.io/docs/akka/2.6/typed/cluster-sharding.html), [Akka Cluster Singleton](https://doc.akka.io/docs/akka/2.6/typed/cluster-singleton.html), [Akka Persistence](https://doc.akka.io/docs/akka/2.6/typed/persistence.html) and [Akka Persistence Query](https://doc.akka.io/docs/akka/2.6/persistence-query.html).

## Overview

This sample application implements a CQRS-ES design that will side-effect in the read model on selected events persisted to Cassandra by the write model. In this sample, the side-effect is logging a line. A more practical example would be to send a message to a Kafka topic or update a relational database.

## Write model

The write model is a shopping cart.

The implementation is based on a sharded actor: each `ShoppingCart` is an [Akka Cluster Sharding](https://doc.akka.io/docs/akka/2.6/typed/cluster-sharding.html) entity. The entity actor `ShoppingCart` is an [EventSourcedBehavior](https://doc.akka.io/docs/akka/2.6/typed/persistence.html).

Events from the shopping carts are tagged and consumed by the read model.

## Read model

The read model is implemented in such a way that 'load' is sharded over a number of processors. This number is `event-processor.parallelism`.

The implementation is resilient: it uses an *Akka Cluster Singleton* in combination with *Akka Cluster Sharding* to always keep the event processors alive.

## Running the sample code

1. Start a Cassandra server by running:

```
sbt "runMain sample.cqrs.Main cassandra"
```

2. Start a node that runs the write model:

```
sbt -Dakka.cluster.roles.0=write-model "runMain sample.cqrs.Main 2551"
```

3. Start a node that runs the read model:

```
sbt -Dakka.cluster.roles.0=read-model "runMain sample.cqrs.Main 2552"
```

4. More write or read nodes can be started started by defining roles and port:

```
sbt -Dakka.cluster.roles.0=write-model "runMain sample.cqrs.Main 2553"
sbt -Dakka.cluster.roles.0=read-model "runMain sample.cqrs.Main 2554"
```

TODO: how to enter or simulate updates
