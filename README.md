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

    self.canvas.create_window(screen_width // 2, option_start_y + len(options) * option_spacing + 30, window=btn_frame)