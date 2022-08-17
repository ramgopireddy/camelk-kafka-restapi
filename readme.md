# Camel K Kafka Basic Quickstart

This example demonstrates how to get started with `Camel K` and `Apache Kafka`. We will show how to quickly set up a Kafka Topic via Red Hat OpenShift Streams for Apache Kafka and be able to use it in a simple Producer/Consumer pattern `Integration`. The producer produces messages to a topic. The consumer then consumes the messages and invokes a simple Rest API.

The quickstart is based on the [Apache Camel K upstream Kafka example](https://github.com/apache/camel-k/tree/main/examples/kafka).


## Preparing the Kafka instance and topic

We assume you have an AMQ Streams cluster provisioned.

**Apache Camel K CLI ("kamel")**

Apart from the support provided by the VS Code extension, you also need the Apache Camel K CLI ("kamel") in order to access all Camel K features.


### Optional Requirements

The following requirements are optional. They don't prevent the execution of the demo, but may make it easier to follow.


## 1. Preparing the project

We'll connect to the `camel-k-kafka` project and check the installation status.

To change project, open a terminal tab and type the following command:

```
oc project camel-k-kafka
```

## 2. Secret preparation

You will have 2 different authentication method available in the next sections: `SASL/Plain` or `SASL/OAUTHBearer`.

### SASL/Plain authentication method

You can take back the secret credentials provided earlier (`kafka bootstrap URL`,`service account id` and `service account secret`). Edit `application.properties` file filling those configuration. Now you can create a secret to contain the sensitive properties in the `application.properties` file that we will pass later to the running `Integration`s:

```
oc create secret generic kafka-props --from-file application.properties
```

## 3. Running a simple Hello Rest API

This is a simple rest API that provides a hello response when invoked

```
kamel run RestDSL.java --dev
```

## 3. Running a Kafka Producer integration

At this stage, run a producer integration. This one will fill the topic with a message, every second:
remove the --dev option if you want to run this in the cluster directly.

```
kamel run --secret kafka-props SaslSSLKafkaProducer.java --dev
```

The producer will create a new message and push into the topic and log some information.


## 4. Running a Kafka Consumer integration

Now, open another shell and run the consumer integration using the command:

```
kamel run --secret kafka-props SaslSSLKafkaConsumer.java --dev
```

A consumer will start logging the events found in the Topic after making the rest API call:

```
[2] 2022-08-17 15:33:28,472 INFO  [FromKafka2Log] (Camel (camel-1) thread #0 - KafkaConsumer[test-topic]) REST api Respose - Hello Message349
[2] 2022-08-17 15:33:29,473 INFO  [FromKafka2Log] (Camel (camel-1) thread #0 - KafkaConsumer[test-topic]) REST api Respose - Hello Message350
[2] 2022-08-17 15:33:30,473 INFO  [FromKafka2Log] (Camel (camel-1) thread #0 - KafkaConsumer[test-topic]) REST api Respose - Hello Message351

```

> **Note:** When you terminate a "dev mode" execution, also the remote integration will be deleted. This gives the experience of a local program execution, but the integration is actually running in the remote cluster. To keep the integration running and not linked to the terminal, you can run it without "dev mode" (`--dev` flag)
