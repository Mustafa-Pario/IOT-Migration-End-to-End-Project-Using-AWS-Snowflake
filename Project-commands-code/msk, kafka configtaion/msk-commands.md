# Apache Kafka Setup Notes

Apache Kafka is used to receive IoT data from **AWS MSK (Managed Kafka)** and stream it into **PostgreSQL** using **Kafka Connect JDBC Sink**. This document explains the complete setup, configuration, and commands used during the project.

---

# 1. Install Java

Kafka requires Java to run. Install Java and `wget`.

```bash
sudo dnf install -y java-17-amazon-corretto wget
```

---

# 2. Download and Extract Kafka

Download Apache Kafka version **3.9.0** and extract it.

```bash
wget https://archive.apache.org/dist/kafka/3.9.0/kafka_2.13-3.9.0.tgz

tar -xzf kafka_2.13-3.9.0.tgz

cd kafka_2.13-3.9.0
```

---

# 3. Configure Kafka Authentication

Create the authentication file used to connect securely with the AWS MSK cluster.

```bash
cat > client-scram.properties << 'EOF'
security.protocol=SASL_SSL
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="iotuser" password="Aircluster@002";
EOF
```

---

# 4. Create a Kafka Topic

Create a topic named **iot-events** with 3 partitions and a replication factor of 3.

```bash
bin/kafka-topics.sh --create \
--topic iot-events \
--bootstrap-server <broker-urls> \
--command-config client-scram.properties \
--partitions 3 \
--replication-factor 3
```

---

# 5. Start the Kafka Producer

The producer sends messages into the Kafka topic.

```bash
bin/kafka-console-producer.sh \
--topic iot-events \
--bootstrap-server <broker-urls> \
--producer.config client-scram.properties
```

After running the command, type a message and press **Enter**.

---

# 6. Start the Kafka Consumer

The consumer reads messages from the Kafka topic.

```bash
bin/kafka-console-consumer.sh \
--topic iot-events \
--bootstrap-server <broker-urls> \
--consumer.config client-scram.properties \
--from-beginning
```

---

# 7. Install the Kafka Connect JDBC Sink Plugin

Download and extract the Confluent JDBC Sink Connector.

```bash
mkdir -p ~/kafka_2.13-3.9.0/connect-plugins

cd ~/kafka_2.13-3.9.0/connect-plugins

wget https://hub-downloads.confluent.io/api/plugins/confluentinc/kafka-connect-jdbc/versions/10.7.4/confluentinc-kafka-connect-jdbc-10.7.4.zip

unzip confluentinc-kafka-connect-jdbc-10.7.4.zip
```

---

# 8. Configure Kafka Connect

Create the Kafka Connect worker configuration.

```bash
cd ~/kafka_2.13-3.9.0

cat > connect-standalone.properties << 'EOF'
bootstrap.servers=<broker-urls>

security.protocol=SASL_SSL
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="iotuser" password="Aircluster@002";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=SCRAM-SHA-512
producer.sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="iotuser" password="Aircluster@002";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=SCRAM-SHA-512
consumer.sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="iotuser" password="Aircluster@002";

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter

key.converter.schemas.enable=true
value.converter.schemas.enable=true

offset.storage.file.filename=/tmp/connect.offsets

plugin.path=/home/ec2-user/kafka_2.13-3.9.0/connect-plugins
EOF
```

---

# 9. Configure the PostgreSQL Sink Connector

Create the JDBC Sink Connector configuration.

```bash
cat > sink-postgres.properties << 'EOF'
name=postgres-sink

connector.class=io.confluent.connect.jdbc.JdbcSinkConnector

tasks.max=1

topics=iot-events

connection.url=jdbc:postgresql://localhost:5432/iot_db
connection.user=iotuser
connection.password=Aircluster@002

auto.create=false
insert.mode=insert

table.name.format=iot_events

pk.mode=none
EOF
```

---

# 10. Start Kafka Connect

Start the Kafka Connect worker.

```bash
nohup bin/connect-standalone.sh \
connect-standalone.properties \
sink-postgres.properties \
> connect.log 2>&1 &
```

---

# 11. Kafka Project Flow

```
IoT Device
      │
      ▼
AWS IoT Core
      │
      ▼
AWS MSK (Kafka Topic: iot-events)
      │
      ▼
Kafka Connect (JDBC Sink)
      │
      ▼
PostgreSQL Database (iot_events Table)
```

---

# Configuration Files

| File | Description |
|------|-------------|
| `client-scram.properties` | Stores AWS MSK authentication settings. |
| `connect-standalone.properties` | Configures the Kafka Connect worker. |
| `sink-postgres.properties` | Configures the PostgreSQL JDBC Sink Connector. |

---

# Project Summary

This setup performs the following tasks:

- Installs Apache Kafka.
- Connects Kafka with AWS MSK using SASL/SCRAM authentication.
- Creates the `iot-events` Kafka topic.
- Sends and receives test messages using the Producer and Consumer.
- Installs the Kafka Connect JDBC Sink plugin.
- Configures Kafka Connect.
- Streams IoT data from AWS MSK into the PostgreSQL `iot_events` table automatically.