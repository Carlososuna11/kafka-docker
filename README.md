# Kafka Docker Configuration

> Kafka is a distributed system consisting of servers and clients that communicate via a high-performance TCP network protocol. It can be deployed on bare-metal hardware, virtual machines, and containers in on-premise as well as cloud environments. [kafka apache](kafla.apache.org)

This repository contains the configuration files for running Kafka in Docker containers. It is based on the [kafka-cofluent](https://developer.confluent.io/quickstart/kafka-docker/) repository.

## Configuration

1. Set up a kafka broker

   The Docker Compose file below will run everything for you via Docker.

```yml
version: "3"
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.0.1
    container_name: zookeeper
    env_file:
      - ./env/zookeeper.env

  broker:
    image: confluentinc/cp-kafka:7.0.1
    container_name: broker
    ports:
      # To learn about configuring Kafka for access across networks see
      # https://www.confluent.io/blog/kafka-client-cannot-connect-to-broker-on-aws-on-docker-etc/
      - "9092:9092"
    depends_on:
      - zookeeper
    env_file:
      - ./env/broker.env
```

2. Start the Kafka broker

   From a directory containing the `docker-compose.yml` file created in the previous step, run this command to start all services in the correct order.

```bash
docker-compose up -d
```

### Create a Topic

   Kafka stores messages in topics. It’s good practice to explicitly create them before using them, even if Kafka is configured to [automagically create them](https://kafka.apache.org/documentation/#brokerconfigs_auto.create.topics.enable) when referenced.

   Run this command to create a new topic into which we’ll write and read some test messages.

```bash
docker exec broker \
kafka-topics --bootstrap-server broker:9092 \
             --create \
             --topic quickstart
```

### Consume a Topic

```
docker exec broker \
kafka-console-consumer --bootstrap-server broker:9092 --topic test --from-beginning
```
