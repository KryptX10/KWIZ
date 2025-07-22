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

        q_text = f"Q{i+1}: {question}"
        user_text = f"Your Answer: {user_answer}"
        correct_text = f"Correct Answer: {correct}"

        self.canvas.create_text(screen_width // 2, y, text=q_text, font=('Arial', font_size_q, 'bold'), fill='black', width=screen_width - 100)
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
    back_btn = tk.Button(self.root, text="Back to Result", font=('Arial', font_size_a + 2), command=self.show_result)
    self.canvas.create_window(screen_width // 2, y + 30, window=back_btn)