### Objective

 To understand the `Processor` interface in Spring Cloud. 
 
 Refer [here](https://github.com/ntarunmenon/spring-cloud-rabbitmq-02) to the code.
 
 
### The code.


```groovy
@SpringBootApplication
@EnableBinding(Processor)
class MyLoggerServiceApplication {


    @StreamListener(Processor.INPUT)
    @SendTo(Processor.OUTPUT)
    Person handle(Person person) {
        println "Received: $person"
        person
    }


    static void main(String[] args) {
        SpringApplication.run(MyLoggerServiceApplication.class, args)
    }
}
``` 
 
 
### What happens at rabbit ? 

- Spring automatically creates an `input` exchange and a queue gets associated with it. 
- Spring automatically creates an `output` exchange. 
- Any message that is published in the `input` exchange automatically gets forwarded to the `output` exchange.       
