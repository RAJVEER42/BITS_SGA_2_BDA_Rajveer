# 📚 Library Management System

A full-stack **Spring Boot** web application that manages Books and Authors with complete CRUD operations, JSP-based views, and a layered architecture following MVC design principles.

> **BITS Pilani | SGA 2 | Big Data Analytics**  
> **Author:** Rajveer Bishnoi

---

## 🧩 Project Overview

This application demonstrates a real-world Spring Boot project with:
- Two JPA entities — **Author** and **Book** — linked by a One-to-Many / Many-to-One relationship
- Full **CRUD operations** (Create, Read, Update) for both entities
- **Custom JPQL inner-join query** to fetch books along with their author details
- **JSP views** for an interactive web UI
- **H2 in-memory database** with auto-seeded sample data
- **Unit tests** for both the repository and service layers

---

## 🏗️ Tech Stack

| Layer | Technology |
|---|---|
| Language | Java 17 |
| Framework | Spring Boot 2.7.18 |
| Persistence | Spring Data JPA + Hibernate |
| Database | H2 In-Memory |
| View Engine | JSP + JSTL |
| Validation | Jakarta Bean Validation |
| Build Tool | Maven |
| Testing | JUnit 5 + Mockito + AssertJ |

---

## 📁 Project Structure

```
library-management/
├── src/
│   ├── main/
│   │   ├── java/com/library/
│   │   │   ├── LibraryManagementApplication.java   # Spring Boot entry point
│   │   │   ├── config/
│   │   │   │   └── DataLoader.java                 # Seeds 10 authors + 10 books on startup
│   │   │   ├── controller/
│   │   │   │   ├── AuthorController.java
│   │   │   │   ├── BookController.java
│   │   │   │   └── HomeController.java
│   │   │   ├── dto/
│   │   │   │   └── BookWithAuthorDTO.java           # DTO for inner-join query result
│   │   │   ├── entity/
│   │   │   │   ├── Author.java                     # @OneToMany → Books
│   │   │   │   └── Book.java                       # @ManyToOne → Author
│   │   │   ├── exception/
│   │   │   │   ├── ResourceNotFoundException.java
│   │   │   │   └── GlobalExceptionHandler.java
│   │   │   ├── repository/
│   │   │   │   ├── AuthorRepository.java
│   │   │   │   └── BookRepository.java
│   │   │   └── service/
│   │   │       ├── AuthorService.java
│   │   │       ├── BookService.java
│   │   │       └── impl/
│   │   │           ├── AuthorServiceImpl.java
│   │   │           └── BookServiceImpl.java
│   │   ├── resources/
│   │   │   └── application.properties
│   │   └── webapp/
│   │       ├── WEB-INF/views/
│   │       │   ├── authors/      # add.jsp, edit.jsp, list.jsp
│   │       │   ├── books/        # add.jsp, edit.jsp, list.jsp, report.jsp
│   │       │   ├── common/       # header.jsp, footer.jsp
│   │       │   ├── error.jsp
│   │       │   └── index.jsp
│   │       └── css/style.css
│   └── test/
│       └── java/com/library/
│           ├── repository/
│           │   ├── AuthorRepositoryTest.java    # 9 tests
│           │   └── BookRepositoryTest.java      # 10 tests
│           └── service/
│               ├── AuthorServiceTest.java       # 8 tests
│               └── BookServiceTest.java         # 9 tests
└── pom.xml
```

---

## 🗃️ Entity Design

### Author
| Field | Type | Constraints |
|---|---|---|
| `id` | Long | Primary Key, Auto-generated |
| `name` | String | Not blank, max 100 chars |
| `email` | String | Unique, valid email, max 150 chars |
| `birthYear` | Integer | 1000–2100 |
| `nationality` | String | Not blank, max 50 chars |

### Book
| Field | Type | Constraints |
|---|---|---|
| `id` | Long | Primary Key, Auto-generated |
| `title` | String | Not blank, max 200 chars |
| `isbn` | String | Unique, 10–20 chars |
| `publicationYear` | Integer | 1000–2100 |
| `genre` | String | Not blank, max 50 chars |
| `author` | Author | ManyToOne (FK: author_id) |

**Relationship:** One Author → Many Books (`@OneToMany` / `@ManyToOne`)

---

## ⚙️ Configuration

| Property | Value |
|---|---|
| Server Port | `8081` |
| DB URL | `jdbc:h2:mem:librarydb` |
| H2 Console | `http://localhost:8081/h2-console` |
| H2 Username | `sa` |
| H2 Password | *(empty)* |
| DDL Auto | `create-drop` |

---

## 🚀 Getting Started

### Prerequisites
- Java 17+
- Maven 3.6+

### Run the Application

```bash
# From the library-management directory
cd library-management
mvn spring-boot:run
```

The app starts at **http://localhost:8081**

> The `DataLoader` automatically seeds **10 Authors** and **10 Books** on every startup.

---

## 🧪 Running Tests

```bash
# From the library-management directory
mvn test

# OR from the root directory
mvn test -f library-management/pom.xml
```

### Test Results

```
Tests run: 37, Failures: 0, Errors: 0, Skipped: 0
BUILD SUCCESS
```

| Test Class | Count | Type |
|---|---|---|
| `AuthorRepositoryTest` | 9 | `@DataJpaTest` (slice test with real H2) |
| `BookRepositoryTest` | 10 | `@DataJpaTest` (slice test with real H2) |
| `AuthorServiceTest` | 8 | Unit test with Mockito |
| `BookServiceTest` | 9 | Unit test with Mockito |

---

## 🌐 Application Endpoints

### Authors
| Method | URL | Description |
|---|---|---|
| GET | `/authors` | List all authors |
| GET | `/authors/add` | Show add author form |
| POST | `/authors/add` | Save new author |
| GET | `/authors/edit/{id}` | Show edit form |
| POST | `/authors/edit/{id}` | Update author |

### Books
| Method | URL | Description |
|---|---|---|
| GET | `/books` | List all books |
| GET | `/books/add` | Show add book form |
| POST | `/books/add` | Save new book |
| GET | `/books/edit/{id}` | Show edit form |
| POST | `/books/edit/{id}` | Update book |
| GET | `/books/report` | Books with author report (inner join) |

---

## 🔍 Custom Query — Inner Join

The `BookRepository` includes a custom JPQL query that performs an **INNER JOIN** between `books` and `authors` tables and maps results to `BookWithAuthorDTO`:

```java
@Query("SELECT new com.library.dto.BookWithAuthorDTO(b.id, b.title, b.isbn, " +
       "b.publicationYear, b.genre, a.name, a.nationality) " +
       "FROM Book b INNER JOIN b.author a")
List<BookWithAuthorDTO> findAllBooksWithAuthors();
```

Accessible via **GET `/books/report`**

---

## 🗄️ H2 Database Console

Access the live database while the app is running:

1. Open `http://localhost:8081/h2-console`
2. Set JDBC URL to `jdbc:h2:mem:librarydb`
3. Username: `sa` | Password: *(leave empty)*
4. Click **Connect**

---

## 📌 Notes

- The H2 database is **in-memory** — data resets on every restart (by design for this assignment)
- Sample data is seeded via `DataLoader.java` using Spring's `CommandLineRunner`
- All service-layer exceptions use `ResourceNotFoundException` (HTTP 404) for missing records
