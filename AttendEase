import sys
import json
import os
from datetime import datetime
from PyQt6.QtWidgets import (
    QApplication, QWidget, QVBoxLayout, QHBoxLayout, QPushButton, QLabel,
    QLineEdit, QMessageBox, QTableWidget, QTableWidgetItem, QDialog
)
from PyQt6.QtCore import Qt
from PyQt6.QtGui import QPixmap, QIcon

DATA_FILE = "attendance_data.json"

def load_data():
    if not os.path.exists(DATA_FILE):
        data = {"users": [{"username": "admin", "password": "admin123", "role": "admin"}], "attendance": []}
        with open(DATA_FILE, "w") as f:
            json.dump(data, f, indent=4)
    with open(DATA_FILE, "r") as f:
        return json.load(f)


def save_data(data):
    with open(DATA_FILE, "w") as f:
        json.dump(data, f, indent=4)


class LoginWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("AttendEase - Login")
        self.setGeometry(500, 200, 350, 350)
        layout = QVBoxLayout()

        logo = QLabel()
        pixmap = QPixmap("attendease_logo.png")
        pixmap = pixmap.scaled(100, 100, Qt.AspectRatioMode.KeepAspectRatio, Qt.TransformationMode.SmoothTransformation)
        logo.setPixmap(pixmap)
        logo.setAlignment(Qt.AlignmentFlag.AlignCenter)

        title = QLabel("<h1 style='color:#0078d7;'>AttendEase</h1>")
        title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        subtitle = QLabel("<h3>Employee Attendance Monitoring System</h3>")
        subtitle.setAlignment(Qt.AlignmentFlag.AlignCenter)

        layout.addWidget(logo)
        layout.addWidget(title)
        layout.addWidget(subtitle)

        # Inputs
        self.username_input = QLineEdit()
        self.username_input.setPlaceholderText("Username")

        self.password_input = QLineEdit()
        self.password_input.setPlaceholderText("Password")
        self.password_input.setEchoMode(QLineEdit.EchoMode.Password)

        self.login_btn = QPushButton("Login")
        self.register_btn = QPushButton("Register New Employee")

        self.login_btn.clicked.connect(self.login)
        self.register_btn.clicked.connect(self.register)

        layout.addWidget(self.username_input)
        layout.addWidget(self.password_input)
        layout.addWidget(self.login_btn)
        layout.addWidget(self.register_btn)
        self.setLayout(layout)

        self.apply_style()

    def apply_style(self):
        self.setStyleSheet("""
            QWidget {
                background-color: #f0f4f8;
                font-family: Segoe UI;
            }
            QLabel {
                color: #1a1a1a;
            }
            QLineEdit {
                border: 1px solid #c2c2c2;
                border-radius: 6px;
                padding: 6px;
                background-color: #ffffff;
            }
            QPushButton {
                background-color: #0078d7;
                color: white;
                border-radius: 6px;
                padding: 8px;
                font-weight: bold;
            }
            QPushButton:hover {
                background-color: #005a9e;
            }
        """)

    def login(self):
        username = self.username_input.text().strip()
        password = self.password_input.text().strip()
        data = load_data()

        user = next((u for u in data["users"] if u["username"] == username and u["password"] == password), None)
        if user:
            if user["role"] == "admin":
                self.admin_panel = AdminPanel()
                self.admin_panel.show()
            else:
                self.employee_panel = EmployeePanel(username)
                self.employee_panel.show()
            self.close()
        else:
            QMessageBox.warning(self, "Login Failed", "Invalid username or password!")

    def register(self):
        self.reg_window = RegisterWindow()
        self.reg_window.show()

class RegisterWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("AttendEase - Register Employee")
        self.setGeometry(550, 250, 350, 250)
        layout = QVBoxLayout()

        label = QLabel("<h3>Register New Employee</h3>")
        label.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(label)

        self.username_input = QLineEdit()
        self.username_input.setPlaceholderText("Username")

        self.password_input = QLineEdit()
        self.password_input.setPlaceholderText("Password")
        self.password_input.setEchoMode(QLineEdit.EchoMode.Password)

        self.register_btn = QPushButton("Register")
        self.register_btn.clicked.connect(self.register_user)

        layout.addWidget(self.username_input)
        layout.addWidget(self.password_input)
        layout.addWidget(self.register_btn)
        self.setLayout(layout)

        self.setStyleSheet("""
            QWidget {
                background-color: #f0f4f8;
                font-family: Segoe UI;
            }
            QLabel { color: #1a1a1a; }
            QLineEdit {
                border: 1px solid #c2c2c2;
                border-radius: 6px;
                padding: 6px;
                background-color: #ffffff;
            }
            QPushButton {
                background-color: #107c10;
                color: white;
                border-radius: 6px;
                padding: 8px;
                font-weight: bold;
            }
            QPushButton:hover { background-color: #0b5c0b; }
        """)

    def register_user(self):
        username = self.username_input.text().strip()
        password = self.password_input.text().strip()
        data = load_data()

        if not username or not password:
            QMessageBox.warning(self, "Error", "All fields are required!")
            return

        if any(u["username"] == username for u in data["users"]):
            QMessageBox.warning(self, "Error", "Username already exists!")
            return

        data["users"].append({"username": username, "password": password, "role": "employee"})
        save_data(data)
        QMessageBox.information(self, "Success", "Employee registered successfully!")
        self.close()

class AdminPanel(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("AttendEase - Admin Panel")
        self.setGeometry(300, 100, 800, 500)
        layout = QVBoxLayout()

        layout.addWidget(QLabel("<h2>All Attendance Records</h2>"))

        self.table = QTableWidget()
        layout.addWidget(self.table)

        btn_layout = QHBoxLayout()
        self.create_btn = QPushButton("Add Record")
        self.update_btn = QPushButton("Update Selected")
        self.delete_btn = QPushButton("Delete Selected")
        self.refresh_btn = QPushButton("Refresh")
        self.logout_btn = QPushButton("Logout")

        for btn in [self.create_btn, self.update_btn, self.delete_btn, self.refresh_btn, self.logout_btn]:
            btn_layout.addWidget(btn)
        layout.addLayout(btn_layout)
        self.setLayout(layout)

        self.setStyleSheet("""
            QWidget { background-color: #e9eef3; font-family: Segoe UI; }
            QTableWidget {
                background-color: #ffffff;
                border: 1px solid #c2c2c2;
                border-radius: 6px;
            }
            QPushButton {
                background-color: #0078d7;
                color: white;
                border-radius: 6px;
                padding: 6px;
            }
            QPushButton:hover { background-color: #005a9e; }
        """)

        self.create_btn.clicked.connect(self.add_record)
        self.update_btn.clicked.connect(self.update_record)
        self.delete_btn.clicked.connect(self.delete_record)
        self.refresh_btn.clicked.connect(self.load_data)
        self.logout_btn.clicked.connect(self.logout)

        self.load_data()

    def load_data(self):
        data = load_data()
        attendance = data["attendance"]
        self.table.setRowCount(len(attendance))
        self.table.setColumnCount(4)
        self.table.setHorizontalHeaderLabels(["Username", "Date", "Check In", "Check Out"])

        for row, record in enumerate(attendance):
            self.table.setItem(row, 0, QTableWidgetItem(record["username"]))
            self.table.setItem(row, 1, QTableWidgetItem(record["date"]))
            self.table.setItem(row, 2, QTableWidgetItem(record.get("check_in", "")))
            self.table.setItem(row, 3, QTableWidgetItem(record.get("check_out", "")))

    def add_record(self):
        dialog = AttendanceDialog()
        if dialog.exec() == QDialog.DialogCode.Accepted:
            username, date, check_in, check_out = dialog.get_data()
            data = load_data()
            data["attendance"].append({
                "username": username, "date": date,
                "check_in": check_in, "check_out": check_out
            })
            save_data(data)
            self.load_data()

    def update_record(self):
        row = self.table.currentRow()
        if row < 0:
            QMessageBox.warning(self, "Error", "No record selected!")
            return
        username = self.table.item(row, 0).text()
        date = self.table.item(row, 1).text()
        check_in = self.table.item(row, 2).text()
        check_out = self.table.item(row, 3).text()

        dialog = AttendanceDialog(username, date, check_in, check_out)
        if dialog.exec() == QDialog.DialogCode.Accepted:
            u, d, ci, co = dialog.get_data()
            data = load_data()
            for record in data["attendance"]:
                if record["username"] == username and record["date"] == date:
                    record["check_in"] = ci
                    record["check_out"] = co
            save_data(data)
            self.load_data()

    def delete_record(self):
        row = self.table.currentRow()
        if row < 0:
            QMessageBox.warning(self, "Error", "No record selected!")
            return
        username = self.table.item(row, 0).text()
        date = self.table.item(row, 1).text()
        data = load_data()
        data["attendance"] = [r for r in data["attendance"] if not (r["username"] == username and r["date"] == date)]
        save_data(data)
        self.load_data()

    def logout(self):
        self.close()
        self.login = LoginWindow()
        self.login.show()

class EmployeePanel(QWidget):
    def __init__(self, username):
        super().__init__()
        self.username = username
        self.setWindowTitle(f"AttendEase - {username}")
        self.setGeometry(400, 150, 600, 400)

        layout = QVBoxLayout()
        layout.addWidget(QLabel(f"<h2>Welcome, {username}</h2>"))

        self.table = QTableWidget()
        layout.addWidget(self.table)

        btn_layout = QHBoxLayout()
        self.checkin_btn = QPushButton("Check In")
        self.checkout_btn = QPushButton("Check Out")
        self.refresh_btn = QPushButton("Refresh")
        self.logout_btn = QPushButton("Logout")

        for btn in [self.checkin_btn, self.checkout_btn, self.refresh_btn, self.logout_btn]:
            btn_layout.addWidget(btn)
        layout.addLayout(btn_layout)
        self.setLayout(layout)

        self.setStyleSheet("""
            QWidget { background-color: #f6f9fc; font-family: Segoe UI; }
            QTableWidget {
                background-color: #ffffff;
                border: 1px solid #ccc;
                border-radius: 6px;
            }
            QPushButton {
                background-color: #0078d7;
                color: white;
                border-radius: 6px;
                padding: 6px;
            }
            QPushButton:hover { background-color: #005a9e; }
        """)

        self.checkin_btn.clicked.connect(self.check_in)
        self.checkout_btn.clicked.connect(self.check_out)
        self.refresh_btn.clicked.connect(self.load_data)
        self.logout_btn.clicked.connect(self.logout)

        self.load_data()

    def load_data(self):
        data = load_data()
        records = [r for r in data["attendance"] if r["username"] == self.username]
        self.table.setRowCount(len(records))
        self.table.setColumnCount(4)
        self.table.setHorizontalHeaderLabels(["Username", "Date", "Check In", "Check Out"])
        for i, r in enumerate(records):
            self.table.setItem(i, 0, QTableWidgetItem(r["username"]))
            self.table.setItem(i, 1, QTableWidgetItem(r["date"]))
            self.table.setItem(i, 2, QTableWidgetItem(r.get("check_in", "")))
            self.table.setItem(i, 3, QTableWidgetItem(r.get("check_out", "")))

    def check_in(self):
        data = load_data()
        today = datetime.now().strftime("%Y-%m-%d")
        if any(r["username"] == self.username and r["date"] == today for r in data["attendance"]):
            QMessageBox.warning(self, "Already Checked In", "You already checked in today!")
            return
        data["attendance"].append({
            "username": self.username,
            "date": today,
            "check_in": datetime.now().strftime("%H:%M:%S"),
            "check_out": ""
        })
        save_data(data)
        self.load_data()

    def check_out(self):
        data = load_data()
        today = datetime.now().strftime("%Y-%m-%d")
        record = next((r for r in data["attendance"] if r["username"] == self.username and r["date"] == today), None)
        if not record:
            QMessageBox.warning(self, "Error", "You need to check in first!")
            return
        record["check_out"] = datetime.now().strftime("%H:%M:%S")
        save_data(data)
        self.load_data()

    def logout(self):
        self.close()
        self.login = LoginWindow()
        self.login.show()

class AttendanceDialog(QDialog):
    def __init__(self, username="", date="", check_in="", check_out=""):
        super().__init__()
        self.setWindowTitle("AttendEase - Attendance Record")
        layout = QVBoxLayout()

        self.username = QLineEdit(username)
        self.date = QLineEdit(date if date else datetime.now().strftime("%Y-%m-%d"))
        self.check_in = QLineEdit(check_in)
        self.check_out = QLineEdit(check_out)

        layout.addWidget(QLabel("Username:"))
        layout.addWidget(self.username)
        layout.addWidget(QLabel("Date (YYYY-MM-DD):"))
        layout.addWidget(self.date)
        layout.addWidget(QLabel("Check In:"))
        layout.addWidget(self.check_in)
        layout.addWidget(QLabel("Check Out:"))
        layout.addWidget(self.check_out)

        btn = QPushButton("Save")
        btn.clicked.connect(self.accept)
        layout.addWidget(btn)
        self.setLayout(layout)

        self.setStyleSheet("""
            QWidget { background-color: #f0f4f8; font-family: Segoe UI; }
            QLineEdit {
                border: 1px solid #c2c2c2;
                border-radius: 6px;
                padding: 6px;
                background-color: #ffffff;
            }
            QPushButton {
                background-color: #0078d7;
                color: white;
                border-radius: 6px;
                padding: 6px;
            }
            QPushButton:hover { background-color: #005a9e; }
        """)

    def get_data(self):
        return self.username.text(), self.date.text(), self.check_in.text(), self.check_out.text()

if __name__ == "__main__":
    app = QApplication(sys.argv)
    app.setWindowIcon(QIcon("attendease_logo.png"))  # App icon
    win = LoginWindow()
    win.show()
    sys.exit(app.exec())
