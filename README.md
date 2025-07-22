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
    view_btn = tk.Button(self.root, text="View Answers", font=('Arial', font_size_button), command=self.view_answers)
    self.canvas.create_window(screen_width // 2, screen_height // 2 + 60, window=view_btn)