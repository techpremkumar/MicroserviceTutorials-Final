# MicroserviceTutorials-Final

A Java-based Spring Boot microservices application demonstrating service discovery, inter-service communication, and API Gateway routing.

## Services

| Service | Responsibility | Default port |
|---|---|---:|
| Service Registry | Eureka service discovery | 8761 |
| Question Service | Question management and answer scoring | 8081 |
| Quiz Service | Quiz creation, question retrieval, and submissions | 8090 |
| API Gateway | Routes client requests to registered services | 8765 |

## Architecture

```text
                         Client
                           |
                           v
                    API Gateway :8765
                      /          \
                     v            v
          Question Service     Quiz Service
               :8081              :8090
                     ^            |
                     |            | OpenFeign
                     +------------+
                           |
                    Eureka Registry
                         :8761

Question Service --> MySQL (questiondb)
Quiz Service     --> MySQL (quizdb)
```

## Technology stack

- Java 21
- Spring Boot
- Spring Cloud Netflix Eureka
- Spring Cloud Gateway
- Spring Cloud OpenFeign
- Spring Data JPA / Hibernate
- MySQL
- Maven

## Prerequisites

- JDK 21
- Maven 3.9+
- MySQL 8+
- IntelliJ IDEA or another Java IDE

## Database setup

Create the databases in MySQL:

```sql
CREATE DATABASE questiondb;
CREATE DATABASE quizdb;
```

Set each service's database URL, username, and password in its local configuration. Do not commit real credentials or OAuth secrets. Use environment variables or ignored local configuration files.

The Eureka client URL should point to:

```properties
eureka.client.service-url.defaultZone=http://localhost:8761/eureka/
```

## Run the services

Run each module in a separate terminal, in this order:

1. `service-registry`
2. `question-service`
3. `quiz-service`
4. `api-gateway`

Example:

```powershell
cd .\service-registry
mvn spring-boot:run
```

Repeat the commands from the repository root for each of the other module directories. Keep each service running in its own terminal.

## API examples

Get all questions directly:

```http
GET http://localhost:8081/question/allQuestions
```

Get all questions through the Gateway:

```http
GET http://localhost:8765/question-service/question/allQuestions
```

Create a quiz:

```http
POST http://localhost:8090/quiz/create
Content-Type: application/json

{
  "categoryName": "Java",
  "numQuestions": 1,
  "title": "Java Fundamentals"
}
```

Get a quiz:

```http
POST http://localhost:8090/quiz/get/1
```

Submit answers:

```http
POST http://localhost:8090/quiz/submit/1
Content-Type: application/json

[
  {
    "id": 1,
    "response": "extends"
  }
]
```

The sample question ID and quiz ID depend on the data in your local databases.

## Repository layout

```text
MicroserviceTutorials-Final/
├── service-registry/
├── question-service/
├── quiz-service/
├── api-gateway/
├── README.md
└── .gitignore
```

Each service is an independent Maven Spring Boot application.

## Security and project hygiene

- Do not commit database passwords, API keys, OAuth client secrets, or other credentials.
- Keep local secrets in environment variables or ignored configuration files.
- Do not commit Maven `target/` output or IDE-local files.
- Ensure the Question Service's quiz-facing response does not expose the correct answer.
