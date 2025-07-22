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