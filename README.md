import tkinter as tk
from tkinter import messagebox
from PIL import Image, ImageTk
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
        self.root.geometry("500x450")

        self.cover_image_name = "coverImage.png"
        self.quiz_image_name = "bgimage.png"
        self.bg_photo = self.load_background(self.cover_image_name)

        self.canvas = tk.Canvas(self.root, width=500, height=450)
        self.canvas.pack(fill="both", expand=True)
        self.bg = self.canvas.create_image(0, 0, anchor="nw", image=self.bg_photo)

        self.exam_duration = 300  # 5 minutes
        self.name_screen()

    def load_background(self, filename):
        try:
            image = Image.open(filename)
            image = image.resize((500, 450))
            return ImageTk.PhotoImage(image)
        except Exception as e:
            messagebox.showerror("Image Error", f"Failed to load '{filename}'.\nError: {e}")
            self.root.destroy()

    def clear(self, exclude_timer=False):
        for widget in self.root.winfo_children():
            if widget != self.canvas and (not exclude_timer or widget != getattr(self, 'timer_label', None)):
                widget.destroy()
        self.canvas.delete("all")
        self.bg = self.canvas.create_image(0, 0, anchor="nw", image=self.bg_photo)

    def name_screen(self):
        self.bg_photo = self.load_background(self.cover_image_name)
        self.clear()
        self.name = ""
        self.selected_subjects = []
        self.current_question_index = 0
        self.score = 0
        self.questions = []
        self.remaining_time = self.exam_duration

        self.name_label = tk.Label(self.root, text="Enter your name:", font=('Arial', 16), bg='white')
        self.name_entry = tk.Entry(self.root, font=('Arial', 14))
        self.next_button = tk.Button(self.root, text="Next", command=self.select_subjects)

        self.canvas.create_window(250, 100, window=self.name_label)
        self.canvas.create_window(250, 140, window=self.name_entry)
        self.canvas.create_window(250, 180, window=self.next_button)

    def select_subjects(self):
        self.name = self.name_entry.get().strip()
        if not self.name:
            messagebox.showerror("Input Error", "Please enter your name.")
            return

        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear()
        self.canvas.create_text(250, 30, text="Select 4 subjects:", font=('Arial', 16), fill='black')

        self.subject_vars = {}
        y = 60
        for subject in questions_data.keys():
            var = tk.IntVar()
            chk = tk.Checkbutton(self.root, text=subject, variable=var, font=('Arial', 12), bg='white')
            self.canvas.create_window(250, y, window=chk)
            self.subject_vars[subject] = var
            y += 30

        start_btn = tk.Button(self.root, text="Start Quiz", command=self.start_quiz)
        self.canvas.create_window(250, y + 10, window=start_btn)

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
            self.timer_label = tk.Label(self.root, text=timer_text, font=('Arial', 12), fg='red', bg='white')
            self.canvas.create_window(400, 20, window=self.timer_label)

    def decrement_timer(self):
        self.remaining_time -= 1
        self.update_timer()
        if self.remaining_time <= 0:
            self.show_result()
        else:
            self.timer = self.root.after(1000, self.decrement_timer)

    def show_question(self):
        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear(exclude_timer=True)
        self.canvas = tk.Canvas(self.root, width=500, height=450)
        self.canvas.pack(fill="both", expand=True)
        self.bg = self.canvas.create_image(0, 0, anchor="nw", image=self.bg_photo)

        if self.current_question_index >= len(self.questions):
            return self.show_result()

        q, ans, options = self.questions[self.current_question_index]
        progress = f"Question {self.current_question_index + 1} of {len(self.questions)}"

        self.canvas.create_text(250, 30, text=progress, font=('Arial', 12), fill='black')
        self.canvas.create_text(250, 70, text=q, font=('Helvetica', 14, 'bold'), fill='black', width=400)

        self.correct_answer = ans
        self.selected_option = tk.StringVar(value="")  # Unselect initially
        y = 120
        for opt in options:
            rb = tk.Radiobutton(self.root, text=opt, variable=self.selected_option, value=opt,
                                font=('Arial', 12), bg='white', anchor='w', width=25, justify='left')
            self.canvas.create_window(250, y, window=rb)
            y += 30

        btn_frame = tk.Frame(self.root, bg='white')
        if self.current_question_index > 0:
            prev_btn = tk.Button(btn_frame, text="Previous", command=self.prev_question)
            prev_btn.pack(side='left', padx=5)
        next_btn = tk.Button(btn_frame, text="Next", command=self.next_question)
        next_btn.pack(side='left', padx=5)

        self.canvas.create_window(250, y + 20, window=btn_frame)

    def next_question(self):
        selected = self.selected_option.get()
        if selected == self.correct_answer:
            self.score += 1
        self.current_question_index += 1
        self.show_question()

    def prev_question(self):
        if self.current_question_index > 0:
            self.current_question_index -= 1
        self.show_question()

    def show_result(self):
        self.root.after_cancel(self.timer)
        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear()
        result_text = f"{self.name}, your score is {self.score} out of {len(self.questions)}"
        self.canvas.create_text(250, 150, text=result_text, font=('Arial', 16), fill='black')
        restart_btn = tk.Button(self.root, text="Restart", command=self.name_screen)
        self.canvas.create_window(250, 200, window=restart_btn)

# Run the app
if __name__ == "__main__":
    root = tk.Tk()
    app = QuizApp(root)
    root.mainloop()