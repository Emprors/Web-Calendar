# Web-Calendar


[Web Calendar]
Learning outcomes
About
Stages
1: What should I do today?
1.1 Description
1.2 Objectives
1.3 Example
2: GET vs POST
2.1 Description
2.2 Objectives
2.3 Examples
3: Relax
3.1 Description
3.2 Objectives
3.3 Examples
4: Back to the future
4.1 Description
4.2 Objectives
4.3 Examples

## Learning outcomes

Сreate a simple REST service with the Spring Boot framework. You will work with a database using Spring Data JPA and create resources using Spring MVC. Learn to use LocalDate objects in Java.

## About

These days, our lives are hectic and eventful. It's tough to keep track of everything that happens around us. Let's create a web calendar to store and manage all upcoming events. Use the Spring Boot framework and create a REST API to save and manage events. The Spring Boot framework allows you to start a web application with a minimal setup.

## Stages

### 1: What should I do today?

#### 1.1 Description

Nowadays, our lives are filled with many events and tasks, making it difficult to keep track of everything. To solve this problem, we can create a web calendar to store and manage all upcoming events. To achieve this, we can use the Spring Boot framework and create a REST API to save and manage events. The Spring Boot framework is perfect for building web applications with minimal setup requirements.


## Theory

Create a Spring Boot application object to start working with it. First, create a Spring project using Spring Boot. The following dependencies are already included in our study project in _build.gradle_ file:

1.  `spring-boot-starter` — the core starter with the autoconfiguration support, logging, and YAML;
    
2.  `spring-boot-starter-web` — the starter for building the web, including `RESTful`, applications using Spring MVC. It uses `Tomcat` as the default embedded container.
    
#### 1.2 Objectives

Create a resource that handles `GET` requests for the `/event/today` endpoint and sends the following JSON object as a response. The endpoint should return the response status code `200`. Run a SpringBoot application on the `28852` port. It is already set in the _application.properties_ of the project.

#### 1.3 Example

**Example 1:** GET request for the /event/today endpoint

_Response:_ `200 OK`

Response body:

```json
[]
```


***

### 2: GET vs POST

#### 2.1 Description

Let's add the ability to handle ```POST``` requests from the user side and parse their arguments. To parse the arguments from the ```POST``` request, use the ```@RequestBody``` annotation. This annotation indicates a method parameter should be bound to the body of the web request. The body of the request is passed through an ```HttpMessageConverter``` to resolve the method argument depending on the content type of the request.

#### 2.2 Objectives


Create a new resource to handle `POST` requests for the _/event_ endpoint. It must require the following arguments in the request body:

-   An `event` argument of the String type.
-   A `date` argument of the `LocalDate` type. The correct format is `YYYY-MM-DD`.

If a user sends the correct response, display the following message: `The event has been added!`, and show the user data:

```json
{ "message": "The event has been added!", "event": "Client event name", "date": "Client date" }
```

#### 2.3 Examples

**Example 1**: _Wrong_ `POST` _request for the /event endpoint_

Request body:

```json
{ "date": "2021-02-10" }
```

_Response:_ `400 Bad Request`

No Response body:

**Example 2**: _Wrong_ `POST` _request for the /event endpoint_

Request body:

```json
{ "event": "Video conference" }
```

_Response:_ `400 Bad Request`

No Response body:

**Example 3**: Сorrect `POST` _request for the /event endpoint_

Request body:

```json
{ "event": "Video conference", "date": "2020-11-15" }
```

_Response:_ `200 OK`

Response body:

```json
{ "message": "The event has been added!", "event": "Video conference", "date": "2020-11-15" }
```

***

### 3: Relax

#### 3.1 Description

What about events that you have added to your calendar? We need to find a way to store and access them whenever you need. Use the Spring Data JPA extension to connect a database to your Spring Boot application. We will use Spring Data JPA to connect our Spring Boot application to an `H2` database.

To start working with Spring Data JPA, we need to add the necessary dependencies to our Gradle-based Spring Boot project:

```java
dependencies { // ... implementation("org.springframework.boot:spring-boot-starter-data-jpa") // ... }
```

Next, we need to configure the database connection in the `application.properties` file (they are already added to the project):

```java
spring.datasource.url=jdbc:h2:file:../d spring.datasource.driverClassName=org.h2.Driver spring.jpa.hibernate.ddl-auto=create-drop spring.jpa.database-platform=org.hibernate.dialect.H2Dialect spring.h2.console.enabled=true spring.h2.console.settings.trace=false spring.h2.console.settings.web-allow-others=false spring.jpa.show-sql=true
```

We also need to create an entity that represents a table in the database. For that, we create a class annotated with `@Entity` and define fields with the appropriate JPA annotations. We can use the `EventRepository` interface, which extends `JpaRepository`, to perform CRUD operations. Now, we can save, delete, and update our data in the database. Also, we need to convert our object into JSON format to send it as a response. Spring Boot does this automatically when we return an object from a controller method.

#### 3.2 Objectives

1.  Create an `Event` entity to save events to the database. The table should contain the following columns:
    
    -   `id` of the INTEGER type. It should be our `PRIMARY KEY`. Its value will be incremented and generated automatically. Starting from 1.
        
    -   `event` of the VARCHAR type. It should be `NOT NULL`.
        
    -   `date` of the DATE type. It should be `NOT NULL`.
        
2.  Your REST API should have the following features:
    
    -   POST request for the `/event` endpoint should save the event to your database. It should require the same arguments as in the previous stage.
        
    -   GET request for the `/event` endpoint should return all the events from the database. If there are no events, it should return the response status code `204 NO_CONTENT`
        
    -   GET request for the `/event/today` endpoint should return the list of today's events.

#### 3.3 Examples

**Example 1**: `GET` _request for the /event endpoint_

_Response:_ `200 OK`

Response body:

```java
[ { "id":1, "event":"Video conference", "date":"2021-03-01" }, { "id":2, "event":"Today's first event", "date":"2021-02-28" } ]
```

**Example 2**: `GET` _request for the /event/today endpoint._

_Response:_ `200 OK`

Response body:

```java
[ { "id":2, "event":"Today's first event", "date":"2021-02-28" } ]
```

***

### 4: Back to the future

#### 4.1 Description

It's easy to forget essential things. Work meetings, doctor appointments, gym sessions, family gatherings, and parties with friends - there's so much to remember! Planning is also crucial for our calendar. We need to add some features like the ability to get a list of events for a specific time interval, find event information by an ID, and delete occurrences from the database. In Spring Boot, you can use the `@PathVariable` annotation to get variables from the request URL.

```java
@GetMapping("/event/{id}") public ResponseEntity<Event> getEventById(@PathVariable("id") Long id) {
	Event event = eventRepository.findById(id);
	if (event == null) {
		return new ResponseEntity<>(HttpStatus.NOT_FOUND);
	 }
		return new ResponseEntity<>(event, HttpStatus.OK);

 }
```

In the example above, we get a Long value from the request URL and return the Event object as a response. If the event does not exist, it will return the 404 status code.

Sometimes, you need to return an error message if something is wrong. You can use the ResponseEntity object to send a response with a given status code and a message.

#### 4.2 Objectives

In this stage, add a resource with the _/event/<int:id>_ URL. It should handle the following requests:

-   A `GET` request should return the event with the ID in JSON format. If an event doesn't exist, return `404` with the following message: `The event doesn't exist!`
    
-   A `DELETE` request should delete the event with the given ID and respond with the following response body with the information on the deleted event:
    
    ```json
    { "id":1, "event":"Video conference", "date":"2020-10-15" }
    ```
    
    If the event with the ID doesn't exist, return `404` with the message `The event doesn't exist!`
    
-   A `GET` request for the _/event_ endpoint with `start_time` and `end_time` parameters should return a list of events for the given time range. If the arguments are missing, return the list of all events.
    
-   The URLs from the previous stage should work in the same way.
    

#### 4.3 Examples

**Example 1**: `GET` _request for the /event?start\_time=2020-10-10&end\_time=2020-10-20 endpoint_

Response Body:

_Response:_ `200 OK`

```json
[ { "id":1, "event":"Video conference", "date":"2020-10-15" }, { "id":2, "event":"Today's first event", "date":"2020-10-20" } ]
```

**Example 2**: `GET` _request for the /event/1 endpoint_

_Response:_ `200 OK`

Response Body:

```json
{ "id":1, "event":"Video conference", "date":"2020-10-15" }
```

**Example 3**: `GET` _request for the /event/10 endpoint_

_Response:_ `404 Not Found`

Response Body:

```json
{ "message": "The event doesn't exist!" }
```

**Example 4**: `DELETE` _request for the /event/1 endpoint_

_Response:_ `200 OK`

Response Body:

```json
{ "id":1, "event":"Video conference", "date":"2020-10-15" }
```

**Example 5**: `DELETE` _request for the /event/10 endpoint_

_Response:_ `404 Not Found`

Response Body:

```json
{ "message": "The event doesn't exist!" }
```

***

