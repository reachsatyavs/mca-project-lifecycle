# Sample Diagrams — Student Course Registration System
> This file shows how to write ER, Class, Flow, and Sequence diagrams
> using Mermaid inside a standard Markdown file.
> GitHub renders these automatically. In VS Code, install the "Mermaid Preview" extension.

---

## 1. ER Diagram

Shows all database entities, their fields, and relationships.

```mermaid
erDiagram
    STUDENT {
        int     student_id   PK
        string  name
        string  email
        string  phone
        string  password_hash
        date    enrolled_on
    }
    FACULTY {
        int     faculty_id   PK
        string  name
        string  email
        string  department
    }
    COURSE {
        int     course_id    PK
        string  title
        int     credits
        int     max_seats
        int     faculty_id   FK
    }
    ENROLLMENT {
        int     enrollment_id  PK
        int     student_id     FK
        int     course_id      FK
        date    enrolled_on
        string  status
    }
    GRADE {
        int     grade_id       PK
        int     enrollment_id  FK
        float   marks
        string  letter_grade
    }

    STUDENT    ||--o{ ENROLLMENT : "registers for"
    COURSE     ||--o{ ENROLLMENT : "has"
    ENROLLMENT ||--|| GRADE      : "results in"
    FACULTY    ||--o{ COURSE     : "teaches"
```

---

## 2. Class Diagram

Shows the software classes, their attributes, methods, and relationships.

```mermaid
classDiagram
    class User {
        +int userId
        +string email
        +string passwordHash
        +login(email, password) bool
        +logout() void
        +resetPassword(token) bool
    }

    class Student {
        +int studentId
        +string name
        +string phone
        +date enrolledOn
        +viewCourses() List~Course~
        +registerCourse(courseId) bool
        +viewGrades() List~Grade~
    }

    class Faculty {
        +int facultyId
        +string name
        +string department
        +viewMyCourses() List~Course~
        +uploadGrade(enrollmentId, marks) void
    }

    class Course {
        +int courseId
        +string title
        +int credits
        +int maxSeats
        +int availableSeats
        +getEnrolledStudents() List~Student~
        +isFull() bool
    }

    class Enrollment {
        +int enrollmentId
        +date enrolledOn
        +string status
        +cancel() void
    }

    class Grade {
        +int gradeId
        +float marks
        +string letterGrade
        +calculate() string
    }

    User <|-- Student  : extends
    User <|-- Faculty  : extends
    Student "1" --> "many" Enrollment : has
    Course  "1" --> "many" Enrollment : contains
    Enrollment "1" --> "1" Grade      : produces
    Faculty "1" --> "many" Course     : teaches
```

---

## 3. Flow Diagram — Course Registration Process

Shows the step-by-step process flow with decision points.

```mermaid
flowchart TD
    A([Student logs in]) --> B[View available courses]
    B --> C[Select a course]
    C --> D{Already registered?}
    D -->|Yes| E[Show error: Already enrolled]
    D -->|No| F{Seats available?}
    F -->|No| G[Show error: Course full]
    F -->|Yes| H{Prerequisites met?}
    H -->|No| I[Show error: Prerequisites missing]
    H -->|Yes| J[Confirm registration]
    J --> K[Create Enrollment record in DB]
    K --> L[Send confirmation email]
    L --> M([Student sees confirmed enrollment])

    E --> B
    G --> B
    I --> B

    style A fill:#1a3a1a,color:#6ee7b7
    style M fill:#1a3a1a,color:#6ee7b7
    style E fill:#3a1a1a,color:#f87171
    style G fill:#3a1a1a,color:#f87171
    style I fill:#3a1a1a,color:#f87171
```

---

## 4. Sequence Diagram — Student Registers for a Course

Shows the order of messages between components for one specific user flow.

```mermaid
sequenceDiagram
    actor Student
    participant Browser
    participant API as REST API
    participant AuthService as Auth Service
    participant DB as Database
    participant Email as Email Service

    Student->>Browser: Enter email + password, click Login
    Browser->>API: POST /auth/login { email, password }
    API->>AuthService: validateCredentials(email, password)
    AuthService->>DB: SELECT * FROM students WHERE email = ?
    DB-->>AuthService: Student record
    AuthService-->>API: JWT token
    API-->>Browser: 200 OK { token }
    Browser-->>Student: Dashboard shown

    Student->>Browser: Click "Register" on CS-401 course
    Browser->>API: POST /enrollment { courseId: 401, token }
    API->>AuthService: verifyToken(token)
    AuthService-->>API: studentId = 12

    API->>DB: SELECT available_seats FROM courses WHERE id = 401
    DB-->>API: available_seats = 5

    API->>DB: INSERT INTO enrollment (student_id, course_id, status)
    DB-->>API: enrollment_id = 88

    API->>DB: UPDATE courses SET available_seats = 4 WHERE id = 401
    DB-->>API: OK

    API->>Email: sendConfirmation(student.email, "CS-401")
    Email-->>API: Sent

    API-->>Browser: 201 Created { enrollmentId: 88 }
    Browser-->>Student: "You are registered for CS-401"
```

---

## How to use this file

| Goal | Steps |
|---|---|
| Preview locally | Install "Mermaid Preview" or "Markdown Preview Enhanced" in VS Code |
| Preview on GitHub | Push to repo — GitHub renders Mermaid automatically |
| Export diagram as image | Open [mermaid.live](https://mermaid.live), paste the code block, download PNG/SVG |
| Export full document as PDF | Use "Markdown Preview Enhanced" → right-click → Export → PDF |
| Convert to Word | Run: `pandoc sample_diagrams.md -o sample_diagrams.docx` |

---

*Replace the entities, classes, and steps above with your actual project details.
The structure stays the same — only the names and fields change.*
