# Kafka Setup Commands

## Step 1: Install Java

```bash
sudo dnf install -y java-17-amazon-corretto wget
```

---

## Step 2: Download and Extract Kafka

```bash
wget https://archive.apache.org/dist/kafka/3.9.0/kafka_2.13-3.9.0.tgz

tar -xzf kafka_2.13-3.9.0.tgz

cd kafka_2.13-3.9.0
```

---

## Step 3: Create Authentication Configuration

```bash
cat > client-scram.properties << 'EOF'
security.protocol=SASL_SSL
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="iotuser" password="Aircluster@002";
EOF
```

---

## Step 4: Create Kafka Topic

```bash
bin/kafka-topics.sh --create \
--topic iot-events \
--bootstrap-server <broker-urls> \
--command-config client-scram.properties \
--partitions 3 \
--replication-factor 3
```

---

## Step 5: Start Kafka Producer

```bash
bin/kafka-console-producer.sh \
--topic iot-events \
--bootstrap-server <broker-urls> \
--producer.config client-scram.properties
```

---

## Step 6: Start Kafka Consumer

```bash
bin/kafka-console-consumer.sh \
--topic iot-events \
--bootstrap-server <broker-urls> \
--consumer.config client-scram.properties \
--from-beginning
```

---

## Step 7: Download JDBC Sink Connector

```bash
mkdir -p ~/kafka_2.13-3.9.0/connect-plugins

cd ~/kafka_2.13-3.9.0/connect-plugins

wget https://hub-downloads.confluent.io/api/plugins/confluentinc/kafka-connect-jdbc/versions/10.7.4/confluentinc-kafka-connect-jdbc-10.7.4.zip

unzip confluentinc-kafka-connect-jdbc-10.7.4.zip
```

---

## Step 8: Configure Kafka Connect

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
value.converter=org.apache.kafka.connect.json.JsonConverter;

key.converter.schemas.enable=true
value.converter.schemas.enable=true

offset.storage.file.filename=/tmp/connect.offsets

plugin.path=/home/ec2-user/kafka_2.13-3.9.0/connect-plugins
EOF
```

---

## Step 9: Configure PostgreSQL Sink Connector

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

## Step 10: Start Kafka Connect

```bash
nohup bin/connect-standalone.sh \
connect-standalone.properties \
sink-postgres.properties \
> connect.log 2>&1 &
```

---


```