---
title: Factory Method Design Pattern in Spring Boot
date: '2024-10-12'
tags: ['java', 'spring-boot', 'design-pattern']
draft: false
summary: 'Let's explore the Factory Method pattern and its implementation in a Spring Boot application'
image: '/static/images/factory-pattern.jpg'
isTop: true
---


The Factory Method pattern is a creational design pattern that defines an interface for creating objects, with the specific class being determined by runtime conditions. Essentially, it allows a class to delegate the responsibility of object instantiation to a specialized Factory class, that decides which concrete implementation to create based on runtime conditions or user input.
This approach promotes loose coupling, enabling our code to be more extendable and maintainable.

In this tutorial, we'll explore the Factory Method pattern and its implementation in a Spring Boot application. 

## 2. Use Cases
The Factory Method pattern is useful when:

- **The type of object to create depends on runtime conditions or user input** - This provides flexibility to the application, allowing it to determine the object type dynamically.
- **We want to encapsulate the instantiation logic within a factory class** - By centralizing object creation in a factory, we avoid duplicating instantiation code across multiple client classes.
- **We want to adhere to the open/closed principle**: The open/closed principle states that software entities should be open for extension but closed for modification. By using the Factory Method pattern, we can introduce new object types with minimal changes to the existing codebase.

## 3. Implementing Factory Method 
To demonstrate the Factory Method pattern, we’ll develop a notification service in Spring Boot that can send various types of notifications (Email, SMS, Push). 
The service decides the type of notification to send based on the user's preferences. This means, we need to decide the type of notification at runtime, and it may differ for each user.


### 3.1. Project Setup
We'll create a Spring Boot project with Maven. To get started, we need to add the following dependencies in the `pom.xml` file:

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

The [spring-boot-starter](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter) dependency provides the core functionality of Spring Boot, while the [spring-boot-starter-test](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test) dependency includes testing libraries.


### 3.2 Define Notification Interface 
Next, we define a `Notification` interface that all notification types will implement. This interface will have a single method, `send()` that sends the notification.
```java
public interface Notification {
    void send();
}
```

### 3.3 Implement Notification Types
We'll create two classes that implement the `Notification` interface: `EmailNotification`, and `PushNotification`. Each class provides a specific implementation for sending the corresponding type of notification.

The `EmailNotification` class implements the `Notification` interface and provides a specific implementation for sending email notifications. The `@Service` annotation registers this class as a Spring Bean, enabling Spring to manage its lifecycle.

```java
@Service
public class EmailNotification implements Notification {
    @Override
    public void send() {
        System.out.println("Sending an Email Notification...");
    }
}
```

Similarly, we define the `SMSNotification` class as well:

```java
@Service
public class SMSNotification implements Notification {
    @Override
    public void send() {
        System.out.println("Sending an SMS Notification...");
    }
}
```

### 3.4. Implement the Factory Method
Next, let's create a class called `NotificationFactory` that will be responsible for creating instances of the appropriate notification type based on the input. This class will use Spring's dependency injection to inject the notification beans and return the correct one based on the input type.

```java
public class NotificationFactory {
    private final EmailNotification emailNotification;
    private final SMSNotification smsNotification;

    public NotificationFactory(EmailNotification emailNotification, SMSNotification smsNotification) {
        this.emailNotification = emailNotification;
        this.smsNotification = smsNotification;
    }
    
    public Notification createNotification(String type) {
        switch (type) {
            case "email":
                return emailNotification;
            case "sms":
                return smsNotification;
            default:
                throw new IllegalArgumentException("Invalid notification type");
        }
    }
}
```
Let's look at the key points in the `NotificationFactory` class:
- **Constructor Injection**: The notification beans (`EmailNotification` and `SMSNotification`) are injected into the factory class through the constructor. 
- **Factory Method**: The `createNotification()` method takes a `type` parameter and returns the appropriate notification bean based on the input. 
- **Exception handling**: If the input is invalid, it throws an `IllegalArgumentException`.

By using dependency injection, the `NotificationFactory` class is decoupled from the concrete notification classes, promoting flexibility and maintainability. At the same time, this reduces the number of objects being created as Spring manages the lifecycle of the notification beans.

### 3.5. Using the Factory Method
Finally, let's create a `NotificationService` class that uses the `NotificationFactory` to send notifications. The `NotificationService` class will have a `sendNotification()` method that takes the notification type as input and sends the appropriate notification.

```java
@Service
public class NotificationService {
    private final NotificationFactory notificationFactory;

    public NotificationService(NotificationFactory notificationFactory) {
        this.notificationFactory = notificationFactory;
    }

    public void sendNotification(String type) {
        Notification notification = notificationFactory.createNotification(type);
        notification.send();
    }
}
```

Once again we use constructor injection to inject the `NotificationFactory` into the `NotificationService` class. The `sendNotification()` method takes the notification type as input, creates the appropriate notification using the factory, and sends it.

It is important to note that the `NotificationService` class is not aware of the concrete notification types (`EmailNotification`, `SMSNotification`). This decoupling ensures that the service class is not affected by changes in the notification types, promoting maintainability and extensibility.



### 4. Testing
Finally, let’s add unit tests to verify the behavior of `NotificationService`. These tests ensure that the service behaves as expected and that the correct notification type is created and sent.

```java
@SpringBootTest
public class NotificationServiceTest {

   @Autowired
   private NotificationService notificationService;

   @MockBean
   private EmailNotification emailNotification;

   @MockBean
   private SMSNotification smsNotification;

   @Test
   public void testEmailNotification() {
      notificationService.sendNotification("email");
      Mockito.verify(emailNotification)
              .send();
   }

   // Similar tests for other notification types
}
```
Here are the key points in the `NotificationServiceTest` class:
- `@SpringBootTest`: This annotation loads the full application context for integration testing. It ensures that all the beans are loaded and available for testing.
- `@MockBean`: Mocks the notification beans, ensuring isolated tests without requiring actual implementations. This allows us to test the service in isolation.
- `Mockito.verify()`: Verifies that the `send()` method is called on the correct notification type. This ensures that the correct notification type is created and sent.
- **Exception Test**: Ensures that passing an invalid type throws an `IllegalArgumentException`, confirming robust error handling.

## 5. Adding a New Notification Type
One of the key benefits of the Factory Method pattern is its extensibility. Let's say we want to add a new notification type, `PushNotification`. To do this, we need to create a new class that implements the `Notification` interface and provide a specific implementation for sending push notifications.

```java
@Service
public class PushNotification implements Notification {
    @Override
    public void send() {
        System.out.println("Sending a Push Notification...");
    }
}
```

Next, we need to update the `NotificationFactory` class to include the new notification type:

```java
public Notification createNotification(String type) {
  switch (type) {
      case "email":
          return emailNotification;
      case "sms":
          return smsNotification;
      case "push":
          return pushNotification;
      default:
          throw new IllegalArgumentException("Invalid notification type");
  }
}
```

As we can see, adding a new notification type is straightforward. We create a new class that implements the `Notification` interface, update the `NotificationFactory` to include the new type, and we're done. This demonstrates the flexibility and extensibility of the Factory Method pattern.

## 6. Conclusion
In this tutorial, we explored the Factory Method design pattern and its implementation in a Spring Boot application. We developed a notification service that uses the Factory Method pattern to create different types of notifications based on user preferences. By delegating the responsibility of object creation to subclasses, we achieved loose coupling, maintainability, and extensibility in our codebase.
Furthermore, we demonstrated how to add a new notification type to the system with minimal changes. 

This pattern is particularly useful when the type of object to create depends on runtime conditions or user input, enabling dynamic object creation and promoting code reusability.
