def show_question(self):
    self.bg_photo = self.load_background(self.quiz_image_name)
    self.clear(exclude_timer=True)

    if self.current_question_index >= len(self.questions):
        return self.show_result()

    screen_width = self.root.winfo_screenwidth()
    screen_height = self.root.winfo_screenheight()

    q, ans, options = self.questions[self.current_question_index]
    self.correct_answer = ans
    self.selected_option = tk.StringVar(value="")

    # Adjust font size based on screen size
    font_size_question = int(screen_height * 0.03)  # ~3% of screen height
    font_size_options = int(screen_height * 0.025)  # ~2.5% of screen height

    # Question number
    self.canvas.create_text(screen_width // 2, screen_height * 0.05, 
                            text=f"Question {self.current_question_index + 1} of {len(self.questions)}", 
                            font=('Arial', font_size_question, 'bold'), fill='black')

    # Question text (wrap and center, with dynamic width)
    self.canvas.create_text(screen_width // 2, screen_height * 0.12, text=q, font=('Arial', font_size_question, 'bold'),
                            fill='black', width=int(screen_width * 0.8))  # 80% of screen width

    # Options spacing
    option_start_y = int(screen_height * 0.18)
    option_spacing = int(screen_height * 0.06)  # Spacing between options

    for i, opt in enumerate(options):
        rb = tk.Radiobutton(self.root, text=opt, variable=self.selected_option, value=opt,
                            font=('Arial', font_size_options), fg='black', bg='white', anchor='w', 
                            width=40, justify='left', padx=10)
        self.canvas.create_window(screen_width // 2, option_start_y + i * option_spacing, window=rb)

    # Navigation buttons (adjust position)
    btn_frame = tk.Frame(self.root, bg='white')
    button_font_size = int(screen_height * 0.02)  # Adjust button text size dynamically

    if self.current_question_index > 0:
        prev_btn = tk.Button(btn_frame, text="Previous", font=('Arial', button_font_size), command=self.prev_question)
        prev_btn.pack(side='left', padx=10)

    next_btn = tk.Button(btn_frame, text="Next", font=('Arial', button_font_size), command=self.next_question)
    next_btn.pack(side='left', padx=10)

    self.canvas.create_window(screen_width // 2, option_start_y + len(options) * option_spacing + 30, window=btn_frame)