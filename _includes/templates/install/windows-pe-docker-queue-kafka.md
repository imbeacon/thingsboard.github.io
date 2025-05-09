
[Apache Kafka](https://kafka.apache.org/) is an open-source stream-processing software platform.

Create docker compose file for ThingsBoard queue service:

```text
docker-compose.yml
```
{: .copy-code}

Add the following line to the yml file. Don’t forget to replace "PUT_YOUR_LICENSE_SECRET_HERE" with your **license secret obtained on the first step**:

```yml
version: '3.2'
services:
  kafka:
    restart: always
    image: bitnami/kafka:3.8.1
    ports:
      - 9092:9092 #to localhost:9092 from host machine
      - 9093 #for Kraft
      - 9094 #to kafka:9094 from within Docker network
    environment:
      ALLOW_PLAINTEXT_LISTENER: "yes"
      KAFKA_CFG_LISTENERS: "OUTSIDE://:9092,CONTROLLER://:9093,INSIDE://:9094"
      KAFKA_CFG_ADVERTISED_LISTENERS: "OUTSIDE://localhost:9092,INSIDE://kafka:9094"
      KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP: "INSIDE:PLAINTEXT,OUTSIDE:PLAINTEXT,CONTROLLER:PLAINTEXT"
      KAFKA_CFG_INTER_BROKER_LISTENER_NAME: "INSIDE"
      KAFKA_CFG_AUTO_CREATE_TOPICS_ENABLE: "false"
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: "1"
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: "1"
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: "1"
      KAFKA_CFG_PROCESS_ROLES: "controller,broker" #KRaft
      KAFKA_CFG_NODE_ID: "0" #KRaft
      KAFKA_CFG_CONTROLLER_LISTENER_NAMES: "CONTROLLER" #KRaft
      KAFKA_CFG_CONTROLLER_QUORUM_VOTERS: "0@kafka:9093" #KRaft
    volumes:
      - kafka-data:/bitnami
  mytbpe:
    restart: always
    image: "thingsboard/tb-pe:{{ site.release.pe_full_ver }}"
    depends_on:
      - kafka
    ports:
      - "8080:8080"
      - "1883:1883"
      - "7070:7070"
      - "5683-5688:5683-5688/udp"
    environment:
      TB_QUEUE_TYPE: kafka
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/thingsboard
      TB_KAFKA_SERVERS: kafka:9094
      TB_LICENSE_SECRET: PUT_YOUR_LICENSE_SECRET_HERE
      TB_LICENSE_INSTANCE_DATA_FILE: /data/license.data
    volumes:
      - mytbpe-data:/data
      - mytbpe-logs:/var/log/thingsboard
  postgres:
    restart: always
    image: "postgres:15"
    ports:
    - "5432"
    environment:
      POSTGRES_DB: thingsboard
      POSTGRES_PASSWORD: postgres
    volumes:
      - mytbpe-data-db:/var/lib/postgresql/data
volumes:
  mytbpe-data:
    external: true
  mytbpe-logs:
    external: true
  mytbpe-data-db:
    external: true
  kafka-data:
    driver: local
```
{: .copy-code}
