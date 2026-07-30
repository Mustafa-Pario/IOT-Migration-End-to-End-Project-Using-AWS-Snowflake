<div align="center">

# 🚀 IoT Migration End To End Project Using AWS & Snowflake

<img width="1660" height="811" alt="Diagram" src="https://github.com/user-attachments/assets/c1211702-48dd-4ce0-929d-4eecfc7541a7" />


### Real-Time IoT Data Pipeline with AWS, Kafka, PostgreSQL, Snowflake, dbt & Streamlit

![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonaws&logoColor=FF9900)
![Kafka](https://img.shields.io/badge/Apache%20Kafka-231F20?style=for-the-badge&logo=apachekafka&logoColor=white)
![Snowflake](https://img.shields.io/badge/Snowflake-29B5E8?style=for-the-badge&logo=snowflake&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![dbt](https://img.shields.io/badge/dbt-FF694B?style=for-the-badge&logo=dbt&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white)

</div>

---

# 📖 Project Overview

This project demonstrates a complete **end-to-end IoT data migration pipeline** built on AWS.

Simulated IoT devices generate sensor data, which is securely sent to AWS IoT Core. The data is streamed through Kafka, stored in PostgreSQL, captured using Debezium CDC, loaded into Snowflake, transformed with dbt, and finally displayed on a live Streamlit dashboard.

---

# 🏗️ Architecture

```text
IoT Devices
      │
      ▼
AWS IoT Core
      │
      ▼
Amazon MSK (Kafka)
      │
      ▼
Kafka Connect (JDBC Sink)
      │
      ▼
PostgreSQL (EC2)
      │
      ▼
Debezium CDC
      │
      ▼
Kafka Topic
      │
      ▼
Python Consumer
      │
      ▼
Snowflake
      │
      ▼
dbt (Bronze → Silver → Gold)
      │
      ▼
Streamlit Dashboard
```

---

# 🔄 Project Workflow

### Step 1 – Generate IoT Data

- Simulated IoT devices generate sensor data.
- Data includes:
  - Device ID
  - Latitude
  - Longitude
  - Timestamp

---

### Step 2 – AWS IoT Core

AWS IoT Core receives the sensor data securely using MQTT.

---

### Step 3 – Amazon MSK (Kafka)

The IoT data is streamed into an Apache Kafka topic hosted on Amazon MSK.

---

### Step 4 – Kafka Connect

Kafka Connect automatically writes incoming Kafka messages into the PostgreSQL database.

---

### Step 5 – PostgreSQL

PostgreSQL stores all IoT events.

---

### Step 6 – Debezium CDC

Debezium monitors PostgreSQL for new inserts and publishes database changes back into Kafka.

---

### Step 7 – Snowflake

A Python consumer reads Kafka messages and loads them into Snowflake.

---

### Step 8 – dbt

dbt transforms the data into:

- Bronze Layer
- Silver Layer
- Gold Layer

---

### Step 9 – Streamlit Dashboard

The dashboard displays:

- Device Locations
- Event Counts
- Device Activity
- Real-Time Analytics

---

# 🛠️ Technologies Used

| Technology | Purpose |
|------------|---------|
| AWS IoT Core | Receive IoT data |
| Amazon MSK | Kafka Streaming |
| Kafka Connect | Move Kafka data to PostgreSQL |
| PostgreSQL | Store IoT events |
| Debezium | Change Data Capture (CDC) |
| Snowflake | Cloud Data Warehouse |
| dbt | Data Transformation |
| Python | Kafka Consumer |
| Streamlit | Dashboard |
| Amazon EC2 | Application Hosting |

---

# 📂 Project Structure

```
IoT-Migration-Project-By-AWS
│
├── AWS-IoT/
├── AWS-MSK/
├── PostgreSQL/
├── Kafka/
├── Debezium/
├── Snowflake/
├── dbt/
├── Streamlit/
├── Images/
├── README.md
└── LICENSE
```

---

# 📊 Dashboard Features

- 📍 Live Device Map
- 📈 Device Activity
- 📊 Event Analytics
- 🛰️ Real-Time Monitoring
- ☁️ Cloud-Based Dashboard

---

# ✨ Project Features

- End-to-End IoT Pipeline
- Real-Time Data Streaming
- Kafka Messaging
- Change Data Capture (CDC)
- Cloud Data Warehouse
- Data Transformation using dbt
- Interactive Dashboard
- AWS Cloud Integration

---

# 🎯 Learning Outcomes

This project demonstrates:

- AWS IoT Core
- Amazon MSK (Kafka)
- Kafka Connect
- PostgreSQL
- Debezium CDC
- Snowflake
- dbt
- Streamlit Dashboard
- End-to-End Data Engineering Pipeline

---
