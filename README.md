# kafka-examples
Ejemplos y recursos para curso de apache kafka


## Arranque de Kafka con Docker Compose

Para iniciar un broker de Kafka en modo desarrollo, utiliza el siguiente comando en la raíz del proyecto:

```bash
docker compose -f docker-compose-debug-commented.yml up
```

Esto levantará un único nodo Kafka (KRaft) con las siguientes propiedades principales:

- **KAFKA_NODE_ID**: 1 — Identificador único del nodo.
- **KAFKA_PROCESS_ROLES**: broker,controller — El nodo actúa como broker y controller.
- **KAFKA_CONTROLLER_QUORUM_VOTERS**: "1@localhost:9093" — Configuración de quorum para KRaft (modo single-node).

## Comandos útiles con Kafka

A continuación algunos comandos básicos usando las utilidades incluidas en la imagen oficial de Kafka (ejecutados dentro del contenedor):

### Acceder al contenedor

```bash
docker exec -it kraft-kafka-debug-commented bash
```

### Crear un topic

```bash
kafka-topics.sh \
- **KAFKA_LISTENERS**: PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093 — Puertos de escucha para clientes y controladores.
- **KAFKA_ADVERTISED_LISTENERS**: PLAINTEXT://localhost:9092 — Dirección anunciada para los clientes.
- **KAFKA_AUTO_CREATE_TOPICS_ENABLE**: "true" — Permite la autocreación de topics (puedes cambiar a "false" para crear topics manualmente).
- **KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR**: "1" — Replicación mínima para temas internos (necesario en single-node).
- **KAFKA_LOG_RETENTION_HOURS**: "24" — Retención de logs en horas (útil para desarrollo).
```

### Listar topics existentes

```bash
kafka-topics.sh --bootstrap-server localhost:9092 --list
```

### Enviar un mensaje a un topic

```bash
echo "Hola Kafka" | kafka-console-producer.sh --bootstrap-server localhost:9092 --topic mi-topic
```

### Leer mensajes de un topic

```bash
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic mi-topic --from-beginning
```

> Puedes consultar más comandos en la [documentación oficial de Apache Kafka](https://kafka.apache.org/documentation/).
- **KAFKA_LOG_DIRS**: ./debug/kafka-data — Carpeta local donde se almacenan los datos de Kafka.

> **Nota:** El puerto 9092 es el que usarán los clientes para conectarse a Kafka desde tu máquina local.

Para detener y eliminar los contenedores:

```bash
docker compose -f docker-compose-debug-commented.yml down
```


## Kafka-ui

Inicio rapido
```bash
docker run -it -p 8080:8080 -e DYNAMIC_CONFIG_ENABLED=true provectuslabs/kafka-ui
```
Para desplegar en modo persistente se necesita un archivo de configuración persistente, se puede utilizxar en de este ejemplo con el siguiente compose
```yml
services:
  kafka-ui:
    container_name: kafka-ui
    image: provectuslabs/kafka-ui:latest
    ports:
      - 8080:8080
    environment:
      DYNAMIC_CONFIG_ENABLED: 'true'
    volumes:
      - ~/kui/config.yml:/etc/kafkaui/dynamic_config.yaml
```


