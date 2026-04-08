# Spring Boot + JSP Project Structures (With Authentication & Authorization, Using JPA)

This document contains **detailed folder structures** for 3 strong projects using **Spring Boot + JSP + Spring Data JPA**:
1. Student Management System
2. Online Quiz System
3. Leave Management System

> All projects include **Authentication (Login/Register)** and **Authorization (Role-based: ADMIN / USER/EMPLOYEE)**

---

# 1. Student Management System

## Project Structure

```
src/main/java/com/example/studentapp
│
├── controller
│   ├── AuthController.java
│   └── StudentController.java
│
├── service
│   ├── AuthService.java
│   ├── StudentService.java
│   ├── impl
│   │   ├── AuthServiceImpl.java
│   │   └── StudentServiceImpl.java
│
├── repository (JPA)
│   ├── UserRepository.java
│   └── StudentRepository.java
│
├── model (Entity)
│   ├── User.java
│   └── Student.java
│
├── dto
│   ├── LoginDTO.java
│   └── StudentDTO.java
│
├── config
│   └── SecurityConfig.java (optional basic security)
│
└── StudentAppApplication.java
```

---

## Database (Entities)

### User
- id
- username
- password
- role

### Student
- id
- name
- email
- course

---

## Auth Logic
- Session-based login or Spring Security
- ADMIN → full access
- USER → view only

---

# 2. Online Quiz System

## Project Structure

```
src/main/java/com/example/quizapp
│
├── controller
│   ├── AuthController.java
│   ├── QuizController.java
│   └── ResultController.java
│
├── service
│   ├── AuthService.java
│   ├── QuizService.java
│   ├── ResultService.java
│   ├── impl
│   │   ├── AuthServiceImpl.java
│   │   ├── QuizServiceImpl.java
│   │   └── ResultServiceImpl.java
│
├── repository (JPA)
│   ├── UserRepository.java
│   ├── QuizRepository.java
│   ├── QuestionRepository.java
│   └── ResultRepository.java
│
├── model (Entity)
│   ├── User.java
│   ├── Quiz.java
│   ├── Question.java
│   └── Result.java
│
├── dto
│   ├── LoginDTO.java
│   ├── QuizDTO.java
│   └── ResultDTO.java
│
├── config
│   └── SecurityConfig.java
│
└── QuizAppApplication.java
```

---

## Database (Entities Relationships)

- User → many Results
- Quiz → many Questions
- Question → belongs to Quiz

---

## Auth Logic
- ADMIN → create quizzes
- USER → attempt quiz
- Protect endpoints using roles

---

# 3. Leave Management System

## Project Structure

```
src/main/java/com/example/leavemanagement
│
├── controller
│   ├── AuthController.java
│   ├── EmployeeController.java
│   └── LeaveController.java
│
├── service
│   ├── AuthService.java
│   ├── EmployeeService.java
│   ├── LeaveService.java
│   ├── impl
│   │   ├── AuthServiceImpl.java
│   │   ├── EmployeeServiceImpl.java
│   │   └── LeaveServiceImpl.java
│
├── repository (JPA)
│   ├── UserRepository.java
│   ├── EmployeeRepository.java
│   └── LeaveRepository.java
│
├── model (Entity)
│   ├── User.java
│   ├── Employee.java
│   └── Leave.java
│
├── dto
│   ├── LoginDTO.java
│   └── LeaveRequestDTO.java
│
├── config
│   └── SecurityConfig.java
│
└── LeaveManagementApplication.java
```

---

## Database (Entities)

### User
- id
- username
- password
- role

### Employee
- id
- name
- leaveBalance

### Leave
- id
- employee (ManyToOne)
- startDate
- endDate
- status

---

## Auth Logic
- EMPLOYEE → apply leave
- ADMIN → approve/reject
- Role-based restrictions using Spring Security or session

---

# Common Features in ALL PROJECTS

- Spring Data JPA (JpaRepository)
- Authentication (Login/Register)
- Authorization (Roles)
- Session / Security
- DTO Layer
- Layered Architecture

---
