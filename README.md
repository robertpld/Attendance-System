# Attendance-System
# ReadMe-Code

import os
import json
import hashlib
from datetime import datetime
import tkinter as tk
from tkinter import simpledialog, messagebox

USERS_FILE = "users.json"
ATTENDANCE_FILE = "attendance.txt"
DEFAULT_PASSWORD = "1234"
DEFAULT_COURSE = "ICT105"

def hash_password(password):
    return hashlib.sha256(password.encode()).hexdigest()

def load_users():
    if os.path.exists(USERS_FILE):
        with open(USERS_FILE, 'r') as file:
            return json.load(file)
    return {}

def save_users(users):
    with open(USERS_FILE, 'w') as file:
        json.dump(users, file, indent=4)

def register_user(user_id, name, email, role):
    users = load_users()
    if user_id in users:
        return False
    users[user_id] = {
        "name": name,
        "email": email,
        "role": role,
        "password": hash_password(DEFAULT_PASSWORD)
    }
    save_users(users)
    return True

def reset_user_password(user_id, new_password):
    users = load_users()
    if user_id in users:
        users[user_id]["password"] = hash_password(new_password)
        save_users(users)
        return True
    return False

def mark_attendance(student_id):
    today = datetime.now().strftime("%Y-%m-%d")
    with open(ATTENDANCE_FILE, 'a') as file:
        file.write(f"{today},{DEFAULT_COURSE},{student_id},P\n")

def get_attendance(filter_by=None, value=None):
    if not os.path.exists(ATTENDANCE_FILE):
        return "No attendance data found."
    lines = []
    with open(ATTENDANCE_FILE, 'r') as file:
        for line in file:
            date, course, sid, status = line.strip().split(',')
            if not filter_by or (filter_by == "student" and sid == value) or (filter_by == "date" and date == value):
                lines.append(f"{date} - {course} - {sid} - {status}")
    return "\n".join(lines) if lines else "No matching records found."

def update_attendance(student_id, date, new_status):
    if not os.path.exists(ATTENDANCE_FILE):
        return False
    updated = False
    lines = []
    with open(ATTENDANCE_FILE, 'r') as file:
        for line in file:
            parts = line.strip().split(',')
            if parts[0] == date and parts[2] == student_id:
                parts[3] = new_status.upper()
                updated = True
            lines.append(",".join(parts))
    with open(ATTENDANCE_FILE, 'w') as file:
        for line in lines:
            file.write(line + "\n")
    return updated

def preload_students():
    sample_users = {
        f"100{i}": {
            "name": name,
            "email": f"student{i}@example.com",
            "role": "student",
            "password": hash_password(DEFAULT_PASSWORD)
        }
        for i, name in enumerate([
            "Alice Smith", "Bob Johnson", "Charlie Brown", "Daisy Lee", "Ethan King",
            "Fiona Wright", "George Adams", "Hannah Moore", "Ivan Clark", "Julia Davis"
        ], start=1)
    }
    users = load_users()
    for sid, info in sample_users.items():
        if sid not in users:
            users[sid] = info
    save_users(users)

class AttendanceApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Attendance System")
        self.create_login_screen()

    def create_login_screen(self):
        self.clear()
        tk.Label(self.root, text="Username").pack()
        self.username = tk.Entry(self.root)
        self.username.pack()

        tk.Label(self.root, text="Password").pack()
        self.password = tk.Entry(self.root, show="*")
        self.password.pack()

        tk.Label(self.root, text="Role").pack()
        self.role = tk.StringVar(value="student")
        tk.OptionMenu(self.root, self.role, "student", "teacher").pack()

        tk.Button(self.root, text="Login", command=self.login).pack(pady=5)
        tk.Button(self.root, text="Register New User", command=self.register).pack()
        tk.Button(self.root, text="Reset Password", command=self.reset_password).pack()

    def login(self):
        uid = self.username.get()
        pwd = self.password.get()
        role = self.role.get()
        users = load_users()
        if uid in users and users[uid]["password"] == hash_password(pwd) and users[uid]["role"] == role:
            if role == "teacher":
                self.show_teacher_dashboard()
            else:
                self.show_student_dashboard(uid)
        else:
            messagebox.showerror("Login Failed", "Incorrect username, password, or role.")

    def register(self):
        uid = simpledialog.askstring("Register", "Enter new user ID:")
        name = simpledialog.askstring("Register", "Enter full name:")
        email = simpledialog.askstring("Register", "Enter email address:")
        role = simpledialog.askstring("Register", "Enter role (student/teacher):")
        if role not in ["student", "teacher"]:
            messagebox.showerror("Error", "Invalid role.")
            return
        if register_user(uid, name, email, role):
            messagebox.showinfo("Success", "User registered successfully.\n(Default password: 1234)")
        else:
            messagebox.showwarning("Duplicate", "That user ID already exists.")

    def reset_password(self):
        uid = simpledialog.askstring("Reset Password", "Enter your user ID:")
        new_pwd = simpledialog.askstring("Reset Password", "Enter new password:", show="*")
        if reset_user_password(uid, new_pwd):
            messagebox.showinfo("Done", "Password updated.")
        else:
            messagebox.showerror("Error", "User not found.")

    def show_teacher_dashboard(self):
        self.clear()
        tk.Label(self.root, text="Teacher Dashboard", font=("Arial", 16)).pack(pady=10)
        tk.Button(self.root, text="View All Attendance", command=self.view_all_attendance).pack(pady=2)
        tk.Button(self.root, text="View by Student ID", command=self.view_by_student).pack(pady=2)
        tk.Button(self.root, text="View by Date", command=self.view_by_date).pack(pady=2)
        tk.Button(self.root, text="Edit Attendance", command=self.edit_attendance_gui).pack(pady=2)
        tk.Button(self.root, text="Logout", command=self.create_login_screen).pack(pady=5)

    def show_student_dashboard(self, sid):
        self.clear()
        tk.Label(self.root, text=f"Welcome {sid}", font=("Arial", 16)).pack(pady=10)
        tk.Button(self.root, text="Mark Attendance", command=lambda: self.mark_attendance_gui(sid)).pack(pady=2)
        tk.Button(self.root, text="View My Attendance", command=lambda: self.view_my_attendance(sid)).pack(pady=2)
        tk.Button(self.root, text="Logout", command=self.create_login_screen).pack(pady=5)

    def mark_attendance_gui(self, sid):
        mark_attendance(sid)
        messagebox.showinfo("Done", "Attendance marked.")

    def view_my_attendance(self, sid):
        records = get_attendance("student", sid)
        messagebox.showinfo("My Attendance", records)

    def view_all_attendance(self):
        records = get_attendance()
        messagebox.showinfo("All Attendance", records)

    def view_by_student(self):
        sid = simpledialog.askstring("Student ID", "Enter student ID:")
        records = get_attendance("student", sid)
        messagebox.showinfo(f"Attendance for {sid}", records)

    def view_by_date(self):
        date = simpledialog.askstring("Date", "Enter date (YYYY-MM-DD):")
        records = get_attendance("date", date)
        messagebox.showinfo(f"Attendance on {date}", records)

    def edit_attendance_gui(self):
        sid = simpledialog.askstring("Student ID", "Enter student ID to edit:")
        date = simpledialog.askstring("Date", "Enter date (YYYY-MM-DD):")
        new_status = simpledialog.askstring("New Status", "Enter new status (P/A):")
        if new_status.upper() in ["P", "A"]:
            success = update_attendance(sid, date, new_status)
            if success:
                messagebox.showinfo("Updated", "Attendance updated successfully.")
            else:
                messagebox.showerror("Error", "Record not found.")
        else:
            messagebox.showerror("Error", "Invalid status. Use 'P' or 'A'.")

    def clear(self):
        for widget in self.root.winfo_children():
            widget.destroy()

if __name__ == "__main__":
    if not os.path.exists(USERS_FILE):
        save_users({
            "admin": {
                "name": "Admin",
                "email": "admin@example.com",
                "role": "teacher",
                "password": hash_password("adminpass")
            }
        })
    preload_students()
    root = tk.Tk()
    app = AttendanceApp(root)
    root.mainloop()
