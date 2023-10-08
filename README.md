# Spring-Cloud-Streams-Kafka
**Création d'une application Spring Cloud Streams avec Kafka :**
<ul>
  <li>Un Service Producer KAFKA via un Rest Controller</li>
  <li>Un Service Consumer KAFKA</li>
  <li>Un Service Supplier KAFKA</li>
  <li>Un Service Function KAFKA</li>
  <li>Un Service de Data Analytics Real Time Stream Processing avec Kaflka Streams</li>
  <li>Une application Web qui permet d'afficher les résultats du Stream Data Analytics en temps réel</li>
</ul>

## Architecture
<div align="center">
<img src="">
</div>

## Les dépendances utilisés 
<div align="center">
<img src="https://github.com/Akasmiou-ouassima/Spring-Cloud-Streams-Kafka/blob/master/captures/D%C3%A9pendances.jpg">
</div>

## Un Service Producer KAFKA via un Rest Controller
```java
@RestController
public class PageEventRestController {
    @Autowired
    private StreamBridge streamBridge;
    @GetMapping("/publish/{topic}/{name}")
    public PageEvent publish(@PathVariable String topic,@PathVariable String name){
        PageEvent pageEvent=new PageEvent(name,Math.random()>0.5?"U1":"U2",new Date(),new Random().nextInt(9000));
        streamBridge.send(topic,pageEvent);
        return pageEvent;
    }
}
```
<div align="center">
<img src="">
</div>

## Un Service Consumer KAFKA
```java
@Service
public class PageEventService {
    @Bean
    public Consumer<PageEvent> pageEventConsumer() {
        return (input) -> {
            System.out.println("******************");
            System.out.println(input.toString());
            System.out.println("******************");
        };
    }
  }
```

## Un Service Supplier KAFKA
```java
@Bean
    public Supplier<PageEvent> pageEventSupplier() {
        return () -> new PageEvent(Math.random() > 0.5 ? "page1" : "page2",
                Math.random() > 0.5 ? "user1" : "user2",
                new Date(),
                new Random().nextInt(9000));
    }
```
## Un Service Function KAFKA
```java
@Bean
    public Function<PageEvent, PageEvent> pageEventFunction() {
        return (input) -> {
            input.setName("Page Event");
            input.setUser("User Event");
            return input;
        };
    }
  ```
## Fichier de configuration
```java
spring.cloud.stream.bindings.pageEventConsumer-in-0.destination=R1
spring.cloud.stream.bindings.pageEventSupplier-out-0.destination=R2
spring.cloud.function.definition=pageEventConsumer;pageEventSupplier;pageEventFunction
spring.cloud.stream.poller.fixed-delay=100
spring.cloud.stream.bindings.pageEventFunction-in-0.destination=R1
spring.cloud.stream.bindings.pageEventFunction-out-0.destination=R3
  ```
