##### What am I doing here ? 

- Create a really simple hello world for spring-cloud-stream-rabbit. I am using Groovy.

##### Run rabbit mq in your local box first

`docker run -d --hostname my-rabbit --name some-rabbit -p 15672:15672 -p 5672:5672 rabbitmq:3-management`

##### The POM 

 ```xml
<properties>
		<java.version>1.8</java.version>
		<spring-cloud.version>Greenwich.SR1</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.codehaus.groovy</groupId>
			<artifactId>groovy</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-stream</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-stream-test-support</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-stream-rabbit</artifactId>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
```

##### The code

```groovy
@ToString(includeNames = true)
   @TupleConstructor
   class Person {
   
       Person () {}
   
       String name
}
```

```groovy
import org.springframework.boot.SpringApplication
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.cloud.stream.annotation.EnableBinding
import org.springframework.cloud.stream.annotation.StreamListener
import org.springframework.cloud.stream.messaging.Processor
import org.springframework.cloud.stream.messaging.Sink

@SpringBootApplication
@EnableBinding(Processor)
class MyLoggerServiceApplication {


    @StreamListener(Sink.INPUT)
    void handle(Person person) {
        println "Received: $person"
    }


    static void main(String[] args) {
        SpringApplication.run(MyLoggerServiceApplication.class, args)
    }
}
```
##### What does the rabbit configuration automatically set up by spring cloud look like ?

Start the rabbit console

http://localhost:15672/#/
admin
admin


- Rabbit set up an exchange called input. The type of the exchange is topic.

![Alt text](exchange.png?raw=true)
 
- There was a binding created to queue  `input.anonymous.gvhfdHVFT6OLl0CA58yVMw` with a routing key #

![Alt text](queue.png?raw=true)


##### How do I test this ?

- Go to Rabbit console at `http://localhost:15672` and user name and password `guest` and `guest`. Click on
Exchanges -> input as Publish message as shown below.

![Alt text](test.png?raw=true)
   
The console log should now print 

```
Received: com.example.demo.Person(name:Sam Spade)
```

