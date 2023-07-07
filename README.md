# Task_Daily-Task-Schedular
import datetime
import tkinter as tk
from tkinter import messagebox, simpledialog

# Task class to store task details
class Task:
    def __init__(self, task_id, task_name, task_date):
        self.task_id = task_id
        self.task_name = task_name
        self.task_date = task_date
        self.task_reminder = None

# User class to manage user-related operations
class User:
    def __init__(self, username, password):
        self.username = username
        self.password = password
        self.tasks = []

    def add_task(self, task_id, task_name, task_date):
        new_task = Task(task_id, task_name, task_date)
        self.tasks.append(new_task)
        messagebox.showinfo("Success", "Task added successfully.")

    def update_task(self, task_id, new_task_name, new_task_date):
        for task in self.tasks:
            if task.task_id == task_id:
                task.task_name = new_task_name
                task.task_date = new_task_date
                messagebox.showinfo("Success", "Task updated successfully.")
                return
        messagebox.showerror("Error", "Task not found.")

    def delete_task(self, task_id):
        for task in self.tasks:
            if task.task_id == task_id:
                self.tasks.remove(task)
                messagebox.showinfo("Success", "Task deleted successfully.")
                return
        messagebox.showerror("Error", "Task not found.")

    def show_tasks(self):
        if not self.tasks:
            messagebox.showinfo("Tasks", "No tasks found.")
        else:
            task_list = "Tasks:\n"
            for task in self.tasks:
                task_list += f"Task ID: {task.task_id}, Name: {task.task_name}, Date: {task.task_date}\n"
            messagebox.showinfo("Tasks", task_list)

    def set_task_reminder(self, task_id, reminder_time):
        for task in self.tasks:
            if task.task_id == task_id:
                task.task_reminder = reminder_time
                messagebox.showinfo("Success", "Task reminder set successfully.")
                return
        messagebox.showerror("Error", "Task not found.")

# Global dictionary to store registered users
users = {}

# Function to register a new user
def register_user():
    username = entry_username.get()
    password = entry_password.get()

    if username in users:
        messagebox.showerror("Error", "Username already exists. Please try again.")
        return

    users[username] = User(username, password)
    messagebox.showinfo("Success", "User registered successfully.")

# Function to log in a user
def login_user():
    username = entry_username.get()
    password = entry_password.get()

    if username not in users or users[username].password != password:
        messagebox.showerror("Error", "Invalid username or password. Please try again.")
        return

    current_user = users[username]
    messagebox.showinfo("Success", f"Welcome, {current_user.username}!")

    # Function to add a task
    def add_task():
        task_id = entry_task_id.get()
        task_date = entry_task_date.get()

        # Validate task ID
        if not task_id:
            messagebox.showerror("Error", "Task ID is required.")
            return

        # Validate task date
        try:
            datetime.datetime.strptime(task_date, "%Y-%m-%d")
        except ValueError:
            messagebox.showerror("Error", "Invalid task date. Please use the format YYYY-MM-DD.")
            return

        task_name = simpledialog.askstring("Task", "Enter the task name:")
        if task_name is None:
            return

        current_user.add_task(task_id, task_name, task_date)
        update_task_list()

    # Function to update a task
    def update_task():
        task_id = entry_task_id.get()
        new_task_date = entry_task_date.get()

        # Validate task ID
        if not task_id:
            messagebox.showerror("Error", "Task ID is required.")
            return

        # Validate task date
        try:
            datetime.datetime.strptime(new_task_date, "%Y-%m-%d")
        except ValueError:
            messagebox.showerror("Error", "Invalid task date. Please use the format YYYY-MM-DD.")
            return

        new_task_name = simpledialog.askstring("Task", "Enter the new task name:")
        if new_task_name is None:
            return

        current_user.update_task(task_id, new_task_name, new_task_date)
        update_task_list()

    # Function to delete a task
    def delete_task():
        task_id = entry_task_id.get()

        # Validate task ID
        if not task_id:
            messagebox.showerror("Error", "Task ID is required.")
            return

        current_user.delete_task(task_id)
        update_task_list()

    # Function to show tasks
    def show_tasks():
        current_user.show_tasks()

    # Function to set task reminder
    def set_task_reminder():
        task_id = entry_task_id.get()

        # Validate task ID
        if not task_id:
            messagebox.showerror("Error", "Task ID is required.")
            return

        # Get the selected task
        selected_task = None
        for task in current_user.tasks:
            if task.task_id == task_id:
                selected_task = task
                break

        if selected_task is None:
            messagebox.showerror("Error", "Task not found.")
            return

        reminder_time = simpledialog.askstring("Task Reminder", "Enter the reminder time (HH:MM):")
        if reminder_time is None:
            return

        # Validate reminder time
        try:
            reminder_time = datetime.datetime.strptime(reminder_time, "%H:%M").time()
        except ValueError:
            messagebox.showerror("Error", "Invalid reminder time. Please use the format HH:MM.")
            return

        current_user.set_task_reminder(task_id, reminder_time)

    def update_task_list():
        task_list.delete(0, tk.END)
        for task in current_user.tasks:
            task_list.insert(tk.END, f"ID: {task.task_id}, Name: {task.task_name}, Date: {task.task_date}")

    # Create main window
    main_window = tk.Toplevel(root)
    main_window.title("Task Manager")
    main_window.geometry("400x500")

    # Create labels and entry fields
    label_task_id = tk.Label(main_window, text="Task ID:")
    label_task_id.pack()
    entry_task_id = tk.Entry(main_window)
    entry_task_id.pack()

    label_task_date = tk.Label(main_window, text="Task Date (YYYY-MM-DD):")
    label_task_date.pack()
    entry_task_date = tk.Entry(main_window)
    entry_task_date.pack()

    # Create buttons
    button_add_task = tk.Button(main_window, text="Add Task", command=add_task)
    button_add_task.pack()

    button_update_task = tk.Button(main_window, text="Update Task", command=update_task)
    button_update_task.pack()

    button_delete_task = tk.Button(main_window, text="Delete Task", command=delete_task)
    button_delete_task.pack()

    button_show_tasks = tk.Button(main_window, text="Show Tasks", command=show_tasks)
    button_show_tasks.pack()

    button_set_reminder = tk.Button(main_window, text="Set Task Reminder", command=set_task_reminder)
    button_set_reminder.pack()

    # Create task list
    task_list = tk.Listbox(main_window)
    task_list.pack()

    update_task_list()

# Create the root window
root = tk.Tk()
root.title("Task Manager")
root.geometry("400x200")

# Create labels and entry fields
label_username = tk.Label(root, text="Username:")
label_username.pack()
entry_username = tk.Entry(root)
entry_username.pack()

label_password = tk.Label(root, text="Password:")
label_password.pack()
entry_password = tk.Entry(root, show="*")
entry_password.pack()

# Create buttons
button_register = tk.Button(root, text="Register", command=register_user)
button_register.pack()

button_login = tk.Button(root, text="Login", command=login_user)
button_login.pack()

# Run the application
root.mainloop()
