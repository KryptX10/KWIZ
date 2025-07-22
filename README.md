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

    def show_question(self):
        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear(exclude_timer=True)

        if self.current_question_index >= len(self.questions):
            return self.show_result()

        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()

        q, ans, options = self.questions[self.current_question_index]
        self.correct_answer = ans
        self.selected_option = None
        self.option_buttons = []

        font_size_question = int(screen_height * 0.03)
        font_size_options = int(screen_height * 0.025)

        self.canvas.create_text(screen_width // 2, screen_height * 0.05,
                                text=f"Question {self.current_question_index + 1} of {len(self.questions)}",
                                font=('Arial', font_size_question, 'bold'), fill='black')

        self.canvas.create_text(screen_width // 2, screen_height * 0.12, text=q,
                                font=('Arial', font_size_question, 'bold'),
                                fill='black', width=int(screen_width * 0.8))

        option_start_y = int(screen_height * 0.18)
        option_spacing = int(screen_height * 0.06)

        def select_option(opt, btn):
            for b in self.option_buttons:
                b.config(bg='white', relief='raised')
            btn.config(bg='lightblue', relief='sunken')
            self.selected_option = opt

        # Create option buttons
        for i, opt in enumerate(options):
            btn = tk.Button(self.root, text=opt,
                            font=('Arial', font_size_options), width=40,
                            anchor='w', justify='left', bg='white', relief='raised')
            btn.config(command=lambda o=opt, b=btn: select_option(o, b))
            self.option_buttons.append(btn)
            self.canvas.create_window(screen_width // 2, option_start_y + i * option_spacing, window=btn)

        # Restore previous selection if any
        previous_selection = self.user_answers[self.current_question_index]
        if previous_selection:
            for btn in self.option_buttons:
                if btn['text'] == previous_selection:
                    btn.config(bg='lightblue', relief='sunken')
                    self.selected_option = previous_selection
                    break

        # Navigation buttons (Previous + Next)
        btn_frame = tk.Frame(self.root, bg='white')

        if self.current_question_index > 0:
            prev_btn = tk.Button(btn_frame, text="Previous", font=('Arial', 14), command=self.prev_question)
            prev_btn.pack(side='left', padx=10)

        next_btn = tk.Button(btn_frame, text="Next", font=('Arial', 14), command=self.next_question)
        next_btn.pack(side='left', padx=10)

        self.canvas.create_window(screen_width // 2, option_start_y + len(options) * option_spacing + 30,
                                  window=btn_frame)

    def show_result(self):
        try:
            self.root.after_cancel(self.timer)
        except Exception:
            pass  # In case timer doesn't exist yet

        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear()

        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()

        score = 0
        for i, answer in enumerate(self.user_answers):
            if answer == self.selected_questions[i][1]:
                score += 1
        self.score = score

        font_size_result = int(screen_height * 0.04)
        font_size_button = int(screen_height * 0.025)

        result_text = f"{self.name}, your score is {self.score} out of {len(self.selected_questions)}"
        self.canvas.create_text(
            screen_width // 2,
            screen_height // 3,
            text=result_text,
            font=('Arial', font_size_result, 'bold'),
            fill='black',
            width=int(screen_width * 0.8)
        )

        # Restart button
        restart_btn = tk.Button(self.root, text="Restart", font=('Arial', font_size_button), command=self.name_screen)
        self.canvas.create_window(screen_width // 2, screen_height // 2, window=restart_btn)

        # View Answers button
        view_btn = tk.Button(self.root, text="View Answers", font=('Arial', font_size_button),
                             command=self.view_answers)
        self.canvas.create_window(screen_width // 2, screen_height // 2 + 60, window=view_btn)

    def view_answers(self):
        self.clear(exclude_timer=True)
        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()

        font_size_q = int(screen_height * 0.025)
        font_size_a = int(screen_height * 0.02)

        y = 40
        spacing = int(screen_height * 0.08)

        self.canvas.create_text(screen_width // 2, 20, text="Review Your Answers",
                                font=('Arial', font_size_q + 4, 'bold'), fill='black')

        for i, (question, correct, options) in enumerate(self.questions):
            user_answer = self.user_answers[i] or "No Answer"
            color = "green" if user_answer == correct else "red"

            q_text = f"Q{i + 1}: {question}"
            user_text = f"Your Answer: {user_answer}"
            correct_text = f"Correct Answer: {correct}"

            self.canvas.create_text(screen_width // 2, y, text=q_text, font=('Arial', font_size_q, 'bold'),
                                    fill='black', width=screen_width - 100)
            y += spacing // 2
            self.canvas.create_text(screen_width // 2, y, text=user_text, font=('Arial', font_size_a), fill=color)
            y += spacing // 2
            self.canvas.create_text(screen_width // 2, y, text=correct_text, font=('Arial', font_size_a), fill='blue')
            y += spacing

            # If screen height is exceeded, show message or enable scrolling in future versions
            if y > screen_height - 100:
                self.canvas.create_text(screen_width // 2, y, text="(More answers not shown due to screen limit)",
                                        font=('Arial', font_size_a), fill='gray')
                break

        # Back to main menu
        back_btn = tk.Button(self.root, text="Back to Result", font=('Arial', font_size_a + 2),
                             command=self.show_result)
        self.canvas.create_window(screen_width // 2, y + 30, window=back_btn)

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
        self.user_answers = [None] * len(self.questions)
        self.start_timer()
        self.show_question()

    def next_question(self):
        self.current_question_index += 1

        if self.current_question_index >= len(self.selected_questions):
            self.show_result()  # Only call the result function here
        else:
            self.show_question()  # Otherwise show the next question

    def prev_question(self):
        self.user_answers[self.current_question_index] = self.selected_option


        if self.current_question_index > 0:
            self.current_question_index -= 1
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

    def clear(self, exclude_timer=False):
        for widget in self.root.winfo_children():
            if widget != self.canvas and (not exclude_timer or widget != getattr(self, 'timer_label', None)):
                widget.destroy()
        self.canvas.delete("all")
        self.bg = self.canvas.create_image(0, 0, anchor="nw", image=self.bg_photo)
