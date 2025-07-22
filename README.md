class QuizApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Quiz App")
        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()
        self.root.geometry(f"{screen_width}x{screen_height}")
        self.root.iconbitmap(resource_path("applogo.ico"))
        self.cover_image_name = "coverImage.png"
        self.quiz_image_name = "bgimage.png"
        self.bg_photo = self.load_background(self.cover_image_name)

        self.canvas = tk.Canvas(self.root, width=500, height=450)
        self.canvas.pack(fill="both", expand=True)
        self.bg = self.canvas.create_image(0, 0, anchor="nw", image=self.bg_photo)

        self.exam_duration = 600
        self.name_screen()

    def load_background(self, image_name):
        try:
            screen_width = self.root.winfo_screenwidth()
            screen_height = self.root.winfo_screenheight()
            bg_image = Image.open(resource_path(image_name))
            bg_image = bg_image.resize((screen_width,screen_height)) #resize to fit window
            return ImageTk.PhotoImage(bg_image)
        except Exception as e:
            messagebox.showerror("Image Error", f"Failed to load '{image_name}'.\nError: {e}")
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
        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()
        self.name = ""
        self.selected_subjects = []
        self.current_question_index = 0
        self.score = 0
        self.questions = []
        self.remaining_time = self.exam_duration

        self.name_label = tk.Label(self.root, text="Enter your name:", font=('Arial', 20), bg='white')
        self.name_entry = tk.Entry(self.root, font=('Arial', 18), width=30)
        self.next_button = tk.Button(self.root, text="Next", font=('Arial', 14), command=self.select_subjects)

        self.canvas.create_window(screen_width // 2, screen_height // 3, window=self.name_label)
        self.canvas.create_window(screen_width // 2, screen_height // 3 + 50, window=self.name_entry)
        self.canvas.create_window(screen_width // 2, screen_height // 3 + 100, window=self.next_button)

    def select_subjects(self):
        self.name = self.name_entry.get().strip()
        if not self.name:
            messagebox.showerror("Input Error", "Please enter your name.")
            return

        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear()

        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()

        self.canvas.create_text(screen_width // 2, 50, text="Select 4 subjects:", font=('Arial', 20), fill='black')

        self.selected_subjects = []
        self.subject_buttons = {}
        subjects = list(questions_data.keys())

        cols = 4
        spacing_x = screen_width // (cols + 1)
        spacing_y = screen_height // 4

        for index, subject in enumerate(subjects):
            row = index // cols
            col = index % cols
            x = spacing_x * (col + 1)
            y = 100 + row * 80

            btn = tk.Button(
                self.root, text=subject, font=('Arial', 14), width=15, bg='white', relief='raised',
                command=lambda s=subject: self.toggle_subject(s)
            )
            self.canvas.create_window(x, y, window=btn)
            self.subject_buttons[subject] = btn

        start_btn = tk.Button(self.root, text="Start Quiz", font=('Arial', 14), command=self.start_quiz)
        self.canvas.create_window(screen_width // 2, screen_height - 100, window=start_btn)

    def toggle_subject(self, subject):
        if subject in self.selected_subjects:
            self.selected_subjects.remove(subject)
            self.subject_buttons[subject].config(bg='white', relief='raised')
        else:
            if len(self.selected_subjects) >= 4:
                messagebox.showwarning("Limit Reached", "You can only select 4 subjects.")
                return
            self.selected_subjects.append(subject)
            self.subject_buttons[subject].config(bg='lightblue', relief='sunken')

    def start_quiz(self):
        if len(self.selected_subjects) != 4:
            messagebox.showerror("Selection Error", "Please select exactly 4 subjects.")
            return

        self.questions = []
        for subj in self.selected_subjects:
            subject_questions = questions_data[subj]
            if len(subject_questions) < 10:
                messagebox.showerror("Data Error", f"Not enough questions in {subj}.")
                return
            self.questions += random.sample(subject_questions, 10)

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

    def select_subjects(self):
        self.name = self.name_entry.get().strip()
        if not self.name:
            messagebox.showerror("Input Error", "Please enter your name.")
            return

        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear()

        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()

        self.canvas.create_text(screen_width // 2, 50, text="Select 4 subjects:", font=('Arial', 20), fill='black')

        self.selected_subjects = []
        self.subject_buttons = {}
        subjects = list(questions_data.keys())

        cols = 4
        spacing_x = screen_width // (cols + 1)
        spacing_y = screen_height // 4

        for index, subject in enumerate(subjects):
            row = index // cols
            col = index % cols
            x = spacing_x * (col + 1)
            y = 100 + row * 80

            btn = tk.Button(
                self.root, text=subject, font=('Arial', 14), width=15, bg='white', relief='raised',
                command=lambda s=subject: self.toggle_subject(s)
            )
            self.canvas.create_window(x, y, window=btn)
            self.subject_buttons[subject] = btn

        start_btn = tk.Button(self.root, text="Start Quiz", font=('Arial', 14), command=self.start_quiz)
        self.canvas.create_window(screen_width // 2, screen_height - 100, window=start_btn)

    def toggle_subject(self, subject):
        if subject in self.selected_subjects:
            self.selected_subjects.remove(subject)
            self.subject_buttons[subject].config(bg='white', relief='raised')
        else:
            if len(self.selected_subjects) >= 4:
                messagebox.showwarning("Limit Reached", "You can only select 4 subjects.")
                return
            self.selected_subjects.append(subject)
            self.subject_buttons[subject].config(bg='lightblue', relief='sunken')

    def start_quiz(self):
        if len(self.selected_subjects) != 4:
            messagebox.showerror("Selection Error", "Please select exactly 4 subjects.")
            return

        self.questions = []
        for subj in self.selected_subjects:
            subject_questions = questions_data[subj]
            if len(subject_questions) < 10:
                messagebox.showerror("Data Error", f"Not enough questions in {subj}.")
                return
            self.questions += random.sample(subject_questions, 10)

        random.shuffle(self.questions)
        self.current_question_index = 0
        self.score = 0
        self.start_timer()
        self.show_question()
        
    def next_question(self):
        if self.selected_option.get() == self.correct_answer:
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

# Run app
if __name__ == "__main__":
    root = tk.Tk()
    app = QuizApp(root)
    root.mainloop()
