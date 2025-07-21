# KWIZ
import tkinter as tk
from tkinter import messagebox
import random

questions_data = {
    "Math": [
        ("What is 2 + 2?", "4", ["3", "4", "5", "6"]),
        ("What is 5 * 3?", "15", ["10", "15", "20", "25"]),
    ],
    "Science": [
        ("What planet is known as the Red Planet?", "Mars", ["Mars", "Earth", "Venus", "Jupiter"]),
        ("What is H2O?", "Water", ["Water", "Oxygen", "Hydrogen", "Carbon"]),
    ],
    "History": [
        ("Who was the first President of the USA?", "George Washington", ["George Washington", "Lincoln", "Adams", "Jefferson"]),
        ("In what year did WWII end?", "1945", ["1945", "1940", "1939", "1950"]),
    ],
    "English": [
        ("What is a synonym of 'Happy'?", "Glad", ["Sad", "Angry", "Glad", "Mad"]),
        ("What is the opposite of 'Cold'?", "Hot", ["Warm", "Cool", "Hot", "Ice"]),
    ],
    "Geography": [
        ("What is the capital of France?", "Paris", ["Paris", "Rome", "London", "Berlin"]),
    ],
    "Computer": [
        ("What does CPU stand for?", "Central Processing Unit", ["Central Processing Unit", "Computer Personal Unit", "Central Program Utility", "Control Processing Unit"]),
    ],
    "Biology": [
        ("What is the powerhouse of the cell?", "Mitochondria", ["Nucleus", "Ribosome", "Mitochondria", "Chloroplast"]),
    ],
    "Chemistry": [
        ("What is the chemical symbol for Gold?", "Au", ["Au", "Ag", "Gd", "Go"]),
    ]
}

class QuizApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Quiz App")
        self.exam_duration = 300  # total time in seconds (5 minutes)
        self.timer = None
        self.name_screen()

    def name_screen(self):
        self.clear()
        self.name = ""
        self.selected_subjects = []
        self.current_question_index = 0
        self.score = 0
        self.questions = []
        self.remaining_time = self.exam_duration

        tk.Label(self.root, text="Enter your name:", font=('Arial', 16)).pack(pady=20)
        self.name_entry = tk.Entry(self.root, font=('Arial', 14))
        self.name_entry.pack()
        tk.Button(self.root, text="Next", command=self.select_subjects).pack(pady=10)

    def select_subjects(self):
        self.name = self.name_entry.get().strip()
        if not self.name:
            messagebox.showerror("Input Error", "Please enter your name.")
            return

        self.clear()
        tk.Label(self.root, text="Select 4 subjects:", font=('Arial', 16)).pack(pady=10)
        self.subject_vars = {}
        for subject in questions_data.keys():
            var = tk.IntVar()
            chk = tk.Checkbutton(self.root, text=subject, variable=var, font=('Arial', 12))
            chk.pack(anchor='w')
            self.subject_vars[subject] = var

        tk.Button(self.root, text="Start Quiz", command=self.start_quiz).pack(pady=10)

    def start_quiz(self):
        selected = [subj for subj, var in self.subject_vars.items() if var.get()]
        if len(selected) != 4:
            messagebox.showerror("Selection Error", "Please select exactly 4 subjects.")
            return

        self.selected_subjects = selected
        self.questions = []
        for subj in self.selected_subjects:
            self.questions += questions_data[subj]
        random.shuffle(self.questions)
        self.current_question_index = 0
        self.score = 0

        self.start_timer()
        self.show_question()

    def start_timer(self):
        self.update_timer()
        self.timer = self.root.after(1000, self.decrement_timer)

    def update_timer(self):
        mins = self.remaining_time // 60
        secs = self.remaining_time % 60
        timer_text = f"Time Left: {mins:02d}:{secs:02d}"
        if hasattr(self, "timer_label"):
            self.timer_label.config(text=timer_text)
        else:
            self.timer_label = tk.Label(self.root, text=timer_text, font=('Arial', 12), fg='red')
            self.timer_label.pack()

    def decrement_timer(self):
        self.remaining_time -= 1
        self.update_timer()
        if self.remaining_time <= 0:
            self.show_result()
        else:
            self.timer = self.root.after(1000, self.decrement_timer)

    def show_question(self):
        self.clear(exclude_timer=True)

        if self.current_question_index >= len(self.questions):
            return self.show_result()

        progress = f"Question {self.current_question_index + 1} of {len(self.questions)}"
        tk.Label(self.root, text=progress, font=('Arial', 12)).pack(pady=5)

        q, ans, options = self.questions[self.current_question_index]
        tk.Label(self.root, text=q, wraplength=400, font=('Arial', 14)).pack(pady=20)

        self.selected_option = tk.StringVar()
        for opt in options:
            tk.Radiobutton(self.root, text=opt, variable=self.selected_option, value=opt, font=('Arial', 12)).pack(anchor='w')

        tk.Button(self.root, text="Next", command=self.next_question).pack(pady=10)

    def next_question(self):
        selected = self.selected_option.get()
        correct_answer = self.questions[self.current_question_index][1]
        if selected == correct_answer:
            self.score += 1

        self.current_question_index += 1
        self.show_question()

    def show_result(self):
        if self.timer:
            self.root.after_cancel(self.timer)
        self.clear()

        total = len(self.questions)
        tk.Label(self.root, text="Quiz Completed!", font=('Arial', 16)).pack(pady=10)
        tk.Label(self.root, text=f"{self.name}, your score: {self.score} / {total}", font=('Arial', 14)).pack(pady=10)

        tk.Button(self.root, text="Retake Quiz", command=self.name_screen).pack(pady=5)
        tk.Button(self.root, text="Exit", command=self.root.quit).pack(pady=5)

    def clear(self, exclude_timer=False):
        for widget in self.root.winfo_children():
            if exclude_timer and hasattr(self, 'timer_label') and widget == self.timer_label:
                continue
            widget.destroy()

# Run the app
if __name__ == "__main__":
    root = tk.Tk()
    root.geometry("500x450")
    app = QuizApp(root)
    root.mainloop()
