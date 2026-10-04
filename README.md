# Quiz Application (Microservices Architecture)

A Spring Boot & Spring Cloud-based Quiz application built using a microservices architecture. It allows dynamic generation and management of quizzes, fetching questions by category, and calculating scores.

---

## 🏗️ Architecture Overview

The system is composed of four decoupled microservices:

1. **Service Discovery (`serviceDiscovery`)**:
   - **Technology**: Netflix Eureka Server
   - **Port**: `8761`
   - **Purpose**: Centralized service registry allowing services to discover each other dynamically without hardcoded hostnames or ports.

2. **API Gateway (`ApiGateway`)**:
   - **Technology**: Spring Cloud Gateway (MVC)
   - **Port**: `8765`
   - **Purpose**: Single entry point for routing client requests to the downstream services registered in Eureka.

3. **Question Service (`quetionsService`)**:
   - **Technology**: Spring Boot, Spring Data JPA, PostgreSQL
   - **Port**: `8080` (default)
   - **Database**: PostgreSQL (`QuestionDB`)
   - **Purpose**: Manages the question bank (CRUD operations, category filtering, random question generation, question wrapper generation, score calculation).

4. **Quiz Service (`QuizService`)**:
   - **Technology**: Spring Boot, Spring Data JPA, OpenFeign, PostgreSQL
   - **Port**: `8090`
   - **Database**: PostgreSQL (`QuizDB`)
   - **Purpose**: Handles quiz creation, quiz question retrieval, and quiz submission via inter-service Feign client communication with `questionService`.

---

## 📁 Project Structure

```text
Quiz_Project/
├── .gitignore
├── README.md
├── serviceDiscovery/
│   ├── src/
│   ├── pom.xml
│   ├── mvnw / mvnw.cmd
│   └── .gitignore
├── ApiGateway/
│   ├── src/
│   ├── pom.xml
│   ├── mvnw / mvnw.cmd
│   └── .gitignore
├── quetionsService/
│   ├── src/
│   ├── pom.xml
│   ├── mvnw / mvnw.cmd
│   └── .gitignore
└── QuizService/
    ├── src/
    ├── pom.xml
    ├── mvnw / mvnw.cmd
    └── .gitignore
```

---

## 🛠️ Prerequisites

- **Java JDK**: Version 17 or higher
- **PostgreSQL**: Running instance on `localhost:5432`
- **Maven**: (Optional, as project includes `mvnw` wrapper in each service)

---

## 🗄️ Database Setup

Create two PostgreSQL databases:

```sql
CREATE DATABASE "QuestionDB";
CREATE DATABASE "QuizDB";
```

Update username and password in each service's `src/main/resources/application.properties` if your PostgreSQL credentials differ from `postgres` / `password`.

---

## 🚀 How to Run

Start the services in the following order:

### 1. Start Service Discovery (Eureka Server)
```bash
cd serviceDiscovery
./mvnw spring-boot:run
```
- Eureka Dashboard: [http://localhost:8761](http://localhost:8761)

### 2. Start Question Service
```bash
cd quetionsService
./mvnw spring-boot:run
```

### 3. Start Quiz Service
```bash
cd QuizService
./mvnw spring-boot:run
```

### 4. Start API Gateway
```bash
cd ApiGateway
./mvnw spring-boot:run
```

---

## 📡 API Endpoints

### Question Service (`/question`)

| HTTP Method | Endpoint | Description |
|---|---|---|
| `GET` | `/question/allQuestions` | Retrieve all questions |
| `GET` | `/question/category/{category}` | Retrieve questions by category |
| `POST` | `/question/add` | Add a new question to the database |
| `GET` | `/question/generate?categoryName={category}&numQuestions={n}` | Generate question IDs for quiz |
| `POST` | `/question/getQuestions` | Fetch question wrappers by list of IDs |
| `POST` | `/question/getScore` | Calculate score given a list of responses |

### Quiz Service (`/quiz`)

| HTTP Method | Endpoint | Description |
|---|---|---|
| `POST` | `/quiz/create` | Create a quiz by category and question count |
| `GET` | `/quiz/get/{id}` | Get all questions for a specific quiz |
| `POST` | `/quiz/submit/{id}` | Submit responses and receive calculated score |

---

## 🧪 Testing & Building

To build any service:
```bash
./mvnw clean compile
```

To run unit / integration tests:
```bash
./mvnw test
```
