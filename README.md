# Student Attendance Management System

## Overview

The Student Attendance Management System is a desktop application developed in Python using the Tkinter GUI library. The system allows teachers and students to manage attendance records through a secure login system with role-based access.

Teachers can view, search, and edit attendance records, while students can mark and view their own attendance. The application stores user information securely using hashed passwords and provides user registration and password reset functionality.

---

## Features

### User Authentication

* Secure login with username, password, and role verification
* Passwords stored using hashing for improved security
* User registration
* Password reset

### Student Functions

* Login as a student
* Mark daily attendance
* View personal attendance records
* Logout

### Teacher Functions

* Login as a teacher
* View all attendance records
* Search attendance by Student ID
* Search attendance by date
* Edit attendance status
* Logout

---

## Technologies Used

* Python 3.x
* Tkinter (GUI)
* JSON (Data Storage)
* hashlib (Password Hashing)
* datetime
* os

---

## Project Structure

```
StudentAttendanceSystem/
│
├── main.py                 # Main application
├── users.json              # User account database
├── attendance.json         # Attendance records
├── README.md               # Project documentation
└── assets/                 # Optional images or icons
```

---

## Installation

### Requirements

* Python 3.9 or later

Tkinter is included with most Python installations.

### Clone the Repository

```bash
git clone https://github.com/yourusername/student-attendance-system.git
cd student-attendance-system
```

### Run the Program

```bash
python main.py
```

---

## Default User Credentials

When a new user is registered:

* Default Password: **1234**

The user should change the password after logging in.

---

## System Workflow

### Student

1. Login
2. Mark Attendance
3. View Attendance History
4. Logout

### Teacher

1. Login
2. View Attendance Records
3. Search by Student
4. Search by Date
5. Edit Attendance
6. Logout

---

## Attendance Status

| Code | Meaning |
| ---- | ------- |
| P    | Present |
| A    | Absent  |

---

## Security Features

* Password hashing using SHA-256
* Role-based access control
* Password reset functionality
* Duplicate user prevention

---

## Future Improvements

* Export attendance to CSV or Excel
* Attendance statistics and reports
* QR code attendance
* Face recognition attendance
* Email notifications
* SQLite or MySQL database integration
* Improved graphical interface using ttk widgets
* Login attempt limitation
* Password strength validation

---

## Learning Outcomes

This project demonstrates:

* Python programming
* Object-Oriented Programming (OOP)
* GUI development with Tkinter
* File handling
* JSON data management
* Password hashing
* User authentication
* Role-based access control
* Basic software engineering principles

---

## Author

**Le Duc Trung Pham**

Information and Communication Technology (ICT)

Student Attendance Management System Project

---

## License

This project is created for educational purposes and may be modified for learning and academic use.
