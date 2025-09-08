Kafka integration:
========================

https://github.com/spring-projects/spring-kafka/discussions/3003


### **How Kafka Integration is Done in the GitHub Discussion**

In [spring-projects/spring-kafka/discussions/3003](https://github.com/spring-projects/spring-kafka/discussions/3003), the user sets up an **integration test** using Spring Boot and Embedded Kafka. Here’s how the integration is structured:

#### **1. Test Setup**
- **Annotations:**  
  - `@SpringBootTest` — Boots up the Spring context for integration testing.
  - `@DirtiesContext` — Ensures a fresh context per test.
  - `@ContextConfiguration` — Supplies a custom configuration class for beans.
  - `@EmbeddedKafka` — Spins up an embedded Kafka broker for test isolation.
    - Sets brokers, topics, SASL_PLAINTEXT security, and disables auto topic creation.

#### **2. Spring Configuration Class**
- **Defines Beans:**
  - `KafkaClientProperties` — Holds bootstrap servers, security protocol, and SASL credentials.
  - `KafkaProducer<String, String>` — Producer bean set up with serialization configs and security properties.
  - `TestConsumer<String, String>` — Consumer bean configured with group, offset reset, deserializers, and security.

#### **3. Test Logic**
- **Autowires Producer and Consumer Beans**
- **Sends Data:**  
  - Uses `KafkaProducer.send(new ProducerRecord<>(...))` to send messages to the topic.
- **Consumes Data:**  
  - Uses custom consumer to poll for records and checks the received keys/values.

---

### **Example: Sending Custom Data to Kafka with Spring Kafka**

Here's a **simplified, production-style example** using Spring Kafka (not raw KafkaProducer) to send custom data (e.g., a POJO) to a Kafka topic.

#### **1. Maven Dependency**
```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>3.1.0</version>
</dependency>
```

#### **2. Define Your Custom Data Class**
```java name=UserEvent.java
package com.example.kafka;

public class UserEvent {
    private String userId;
    private String action;
    // getters, setters, toString...
}
```

#### **3. Producer Configuration**
```java name=KafkaProducerConfig.java
package com.example.kafka;

import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.*;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class KafkaProducerConfig {
    @Bean
    public ProducerFactory<String, UserEvent> producerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, org.springframework.kafka.support.serializer.JsonSerializer.class);
        return new DefaultProducerFactory<>(config);
    }

    @Bean
    public KafkaTemplate<String, UserEvent> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}
```

#### **4. Sending Custom Data**
```java name=UserEventProducer.java
package com.example.kafka;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;

@Service
public class UserEventProducer {

    @Autowired
    private KafkaTemplate<String, UserEvent> kafkaTemplate;

    public void sendUserEvent(UserEvent event) {
        kafkaTemplate.send("user-events-topic", event.getUserId(), event);
    }
}
```

#### **5. Usage Example**
```java name=SendUserEventDemo.java
@Autowired
private UserEventProducer producer;

public void sendEventExample() {
    UserEvent event = new UserEvent();
    event.setUserId("abc123");
    event.setAction("login");
    producer.sendUserEvent(event);
}
```

---

### **Summary**
- The GitHub discussion sets up an integration test using Embedded Kafka and raw KafkaProducer/Consumer beans, with SASL security.
- To send custom data in a real project, use Spring Kafka’s `KafkaTemplate` with a POJO and a JSON serializer.
- You can easily produce custom events to a Kafka topic by injecting and using `KafkaTemplate`.


