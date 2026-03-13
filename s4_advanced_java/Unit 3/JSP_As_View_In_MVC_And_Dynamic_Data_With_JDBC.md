# JSP as View in MVC & Dynamic Data with JDBC

---

## SECTION A: Using JSP as a View in MVC

---

### 1. What is MVC Architecture?

MVC stands for **Model – View – Controller**. It is a **design pattern** that separates an application into 3 parts:

| Layer | Component in Java Web App | Role |
|---|---|---|
| **Model** | Java Bean / POJO class | Holds data (e.g., Student, Product) |
| **View** | JSP page | Displays data to the user (UI) |
| **Controller** | Servlet | Handles request, calls model, forwards to view |

> **One-line definition:** MVC separates business logic (Controller), data (Model), and presentation (View) to make code clean, reusable, and maintainable.

---

### 2. MVC Flow Diagram

```
User (Browser)
     │
     │  HTTP Request (form submit / URL)
     ▼
┌─────────────┐
│  CONTROLLER  │  ← Servlet (e.g., LoginServlet.java)
│  (Servlet)   │     - Reads request params
└──────┬───────┘     - Calls Model (business logic)
       │              - Sets data as attributes
       │              - Forwards to View (JSP)
       │
  ┌────┴────┐
  ▼         ▼
┌──────┐  ┌──────┐
│MODEL │  │ VIEW │  ← JSP page (e.g., result.jsp)
│(Bean)│  │(JSP) │     - Uses EL / JSTL to display data
└──────┘  └──────┘
               │
               │  HTML Response
               ▼
         User (Browser)
```

### Request Flow (Step-by-Step):

1. User submits a form → **HTTP Request** goes to Servlet
2. **Servlet (Controller)** reads form data using `request.getParameter()`
3. Servlet calls the **Model** (Java class) to process/fetch data
4. Servlet stores result using `request.setAttribute("key", value)`
5. Servlet **forwards** the request to a **JSP (View)**
6. **JSP** reads data using EL (`${key}`) and displays HTML
7. **HTML Response** sent back to browser

---

### 3. Why JSP as View? (Not Servlet)

| Reason | Explanation |
|---|---|
| Cleaner code | JSP lets you write HTML naturally; Servlet needs `out.println()` |
| Separation of concerns | Developer writes Servlet logic; Designer writes JSP |
| EL + JSTL | JSP works perfectly with EL and JSTL for dynamic display |
| Maintainability | Changing UI doesn't require changing business logic |

---

### 4. Complete MVC Example — Student Login

#### Step 1: Model — `Student.java` (Java Bean)

```java
public class Student {
    private int id;
    private String name;
    private int marks;

    // Constructors
    public Student() {}
    public Student(int id, String name, int marks) {
        this.id = id;
        this.name = name;
        this.marks = marks;
    }

    // Getters and Setters
    public int getId() { return id; }
    public String getName() { return name; }
    public int getMarks() { return marks; }

    public void setId(int id) { this.id = id; }
    public void setName(String name) { this.name = name; }
    public void setMarks(int marks) { this.marks = marks; }
}
```

#### Step 2: Controller — `StudentServlet.java`

```java
@WebServlet("/student")
public class StudentServlet extends HttpServlet {

    protected void doGet(HttpServletRequest request, 
                         HttpServletResponse response)
                         throws ServletException, IOException {

        // 1. Get parameter from URL/form
        String name = request.getParameter("name");

        // 2. Create Model object (normally fetched from DB)
        Student s = new Student(1, name, 85);

        // 3. Set data as request attribute
        request.setAttribute("student", s);

        // 4. Forward to JSP View
        RequestDispatcher rd = request.getRequestDispatcher("result.jsp");
        rd.forward(request, response);
    }
}
```

#### Step 3: View — `result.jsp`

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head><title>Student Result</title></head>
<body>

    <h2>Student Information</h2>

    <p>ID: ${student.id}</p>
    <p>Name: ${student.name}</p>
    <p>Marks: ${student.marks}</p>

    <c:choose>
        <c:when test="${student.marks >= 75}">
            <p>Result: <b>Distinction </b></p>
        </c:when>
        <c:when test="${student.marks >= 40}">
            <p>Result: <b>Pass </b></p>
        </c:when>
        <c:otherwise>
            <p>Result: <b>Fail </b></p>
        </c:otherwise>
    </c:choose>

</body>
</html>
```

---

### 5. `forward` vs `redirect` — Exam Favourite!

| Feature | `forward` (RequestDispatcher) | `redirect` (sendRedirect) |
|---|---|---|
| Done by | **Server** | **Client** (browser) |
| URL in browser | **Does NOT change** | **Changes** |
| Request/response objects | **Same** (preserved) | New (lost) |
| Speed | **Faster** | Slower (2 round trips) |
| Used in MVC for | Forwarding to JSP View | Going to a different resource |
| Syntax | `rd.forward(req, res)` | `response.sendRedirect("url")` |

---

### 6. `web.xml` — Servlet Mapping (if not using annotations)

```xml
<servlet>
    <servlet-name>StudentServlet</servlet-name>
    <servlet-class>com.example.StudentServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>StudentServlet</servlet-name>
    <url-pattern>/student</url-pattern>
</servlet-mapping>
```

---
---

## SECTION B: Displaying Dynamic Data Using JSP + JDBC

---

### 1. What is JDBC?

**JDBC (Java Database Connectivity)** is a Java API that lets Java programs **connect and interact with databases** (MySQL, Oracle, etc.).

> **In JSP context:** JDBC is used to fetch data from a database and display it dynamically on a JSP page via Servlet (MVC pattern).

---

### 2. JDBC Steps (Must Memorize — 5 Steps)

```
① Load Driver       → Class.forName("com.mysql.cj.jdbc.Driver")
② Get Connection    → DriverManager.getConnection(url, user, pass)
③ Create Statement  → con.createStatement() or con.prepareStatement()
④ Execute Query     → stmt.executeQuery("SELECT ...")
⑤ Close Connection  → con.close()
```

---

### 3. JDBC with JSP + MVC — Complete Example

**Scenario:** Display all students from a MySQL database in a table on JSP.

#### Database Setup (MySQL):
```sql
CREATE DATABASE college;
USE college;

CREATE TABLE student (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50),
    marks INT
);

INSERT INTO student VALUES (1, 'Ankit', 82);
INSERT INTO student VALUES (2, 'Priya', 55);
INSERT INTO student VALUES (3, 'Raj', 35);
```

---

#### Model — `Student.java` (same as before)
```java
public class Student {
    private int id;
    private String name;
    private int marks;
    
    public Student(int id, String name, int marks) {
        this.id = id; this.name = name; this.marks = marks;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public int getMarks() { return marks; }
}
```

---

#### DAO (Data Access Object) — `StudentDAO.java`

> DAO = a helper class that handles all database operations. Keeps DB code separate from Servlet.

```java
import java.sql.*;
import java.util.*;

public class StudentDAO {

    private static final String URL  = "jdbc:mysql://localhost:3306/college";
    private static final String USER = "root";
    private static final String PASS = "password";

    public List<Student> getAllStudents() {
        List<Student> list = new ArrayList<>();

        try {
            // Step 1: Load Driver
            Class.forName("com.mysql.cj.jdbc.Driver");

            // Step 2: Get Connection
            Connection con = DriverManager.getConnection(URL, USER, PASS);

            // Step 3: Create Statement
            Statement stmt = con.createStatement();

            // Step 4: Execute Query
            ResultSet rs = stmt.executeQuery("SELECT * FROM student");

            // Process results
            while (rs.next()) {
                int id       = rs.getInt("id");
                String name  = rs.getString("name");
                int marks    = rs.getInt("marks");
                list.add(new Student(id, name, marks));
            }

            // Step 5: Close
            con.close();

        } catch (Exception e) {
            e.printStackTrace();
        }

        return list;
    }
}
```

---

#### Controller — `StudentServlet.java`

```java
@WebServlet("/students")
public class StudentServlet extends HttpServlet {

    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
                         throws ServletException, IOException {

        // Call DAO to get data from DB
        StudentDAO dao = new StudentDAO();
        List<Student> students = dao.getAllStudents();

        // Store in request scope
        request.setAttribute("students", students);

        // Forward to JSP
        request.getRequestDispatcher("students.jsp").forward(request, response);
    }
}
```

---

#### View — `students.jsp`

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head><title>Student List</title></head>
<body>

<h2>All Students</h2>

<table border="1" cellpadding="8">
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Marks</th>
        <th>Result</th>
    </tr>

    <c:forEach var="s" items="${students}">
        <tr>
            <td>${s.id}</td>
            <td>${s.name}</td>
            <td>${s.marks}</td>
            <td>
                <c:choose>
                    <c:when test="${s.marks >= 75}">Distinction</c:when>
                    <c:when test="${s.marks >= 40}">Pass</c:when>
                    <c:otherwise>Fail</c:otherwise>
                </c:choose>
            </td>
        </tr>
    </c:forEach>
</table>

<c:if test="${empty students}">
    <p>No students found.</p>
</c:if>

</body>
</html>
```

---

### 4. PreparedStatement vs Statement

| Feature | `Statement` | `PreparedStatement` |
|---|---|---|
| Query | Written with string concat | Uses `?` placeholders |
| SQL Injection | **Vulnerable** | **Safe** |
| Performance | Slower (re-compiled each time) | **Faster** (pre-compiled) |
| Use case | Static queries | Dynamic queries with params |

**PreparedStatement Example (Search by ID):**
```java
String query = "SELECT * FROM student WHERE id = ?";
PreparedStatement ps = con.prepareStatement(query);
ps.setInt(1, studentId);      // Replace ? with value
ResultSet rs = ps.executeQuery();
```

---

### 5. JSP + JDBC Without MVC (Direct in JSP — Not Recommended but Exam May Ask)

```jsp
<%@ page import="java.sql.*" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html><body>

<h2>Student List</h2>
<table border="1">
<tr><th>ID</th><th>Name</th><th>Marks</th></tr>

<%
    Class.forName("com.mysql.cj.jdbc.Driver");
    Connection con = DriverManager.getConnection(
        "jdbc:mysql://localhost:3306/college", "root", "password");
    Statement stmt = con.createStatement();
    ResultSet rs = stmt.executeQuery("SELECT * FROM student");

    while (rs.next()) {
%>
    <tr>
        <td><%= rs.getInt("id") %></td>
        <td><%= rs.getString("name") %></td>
        <td><%= rs.getInt("marks") %></td>
    </tr>
<%
    }
    con.close();
%>

</table>
</body></html>
```

> **Exam Note:** This approach (DB code directly in JSP) violates MVC. Always prefer the Servlet + DAO + JSP approach. But know this for 2-mark or code-reading questions.

---

### 6. Required JARs (add to `WEB-INF/lib/`)

| JAR | Purpose |
|---|---|
| `mysql-connector-j-x.x.x.jar` | MySQL JDBC driver |
| `jstl-1.2.jar` | JSTL support |

---

## Quick Revision Q&A

**Q1: What is MVC? How is it implemented in Java Web Apps?**
> MVC = Model-View-Controller. In Java web apps: **Model** = Java Bean/POJO, **View** = JSP page, **Controller** = Servlet. The Servlet receives requests, uses Model for data, and forwards to JSP for display.

**Q2: Why should JSP be used as View and not Controller?**
> JSP is designed for presentation (HTML output). Business logic in JSP makes it messy and hard to maintain. The Servlet (Controller) handles logic and forwards processed data to JSP (View) for clean display.

**Q3: What is the difference between forward and sendRedirect?**
> `forward` is done server-side — URL stays same, request/response objects preserved, faster. `sendRedirect` makes the browser send a new request — URL changes, data in request is lost, slower.

**Q4: What are the 5 steps of JDBC?**
> ① Load Driver (`Class.forName`) → ② Get Connection (`DriverManager.getConnection`) → ③ Create Statement → ④ Execute Query (`executeQuery` / `executeUpdate`) → ⑤ Close Connection

**Q5: What is a DAO class?**
> DAO (Data Access Object) is a design pattern where all database operations are placed in a separate class. The Servlet calls the DAO instead of writing DB code directly — keeps code clean and maintainable.

**Q6: What is the difference between Statement and PreparedStatement?**
> `Statement` is used for static queries; it's vulnerable to SQL injection. `PreparedStatement` uses `?` placeholders, is pre-compiled, faster for repeated queries, and safe from SQL injection.

**Q7: Write the JDBC URL for MySQL.**
```
jdbc:mysql://localhost:3306/databaseName
```

---
