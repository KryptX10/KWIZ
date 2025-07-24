def view_answers(self):
    self.clear(exclude_timer=True)
    self.bg_photo = self.load_background(self.quiz_image_name)

    screen_width = self.root.winfo_screenwidth()
    screen_height = self.root.winfo_screenheight()

    font_size_q = int(screen_height * 0.025)
    font_size_a = int(screen_height * 0.02)

    # Create a scrollable frame
    container = tk.Frame(self.root, bg="white")
    canvas = tk.Canvas(container, bg="white", width=screen_width, highlightthickness=0)
    scrollbar = tk.Scrollbar(container, orient="vertical", command=canvas.yview)
    scroll_frame = tk.Frame(canvas, bg="white")

    scroll_frame.bind(
        "<Configure>",
        lambda e: canvas.configure(scrollregion=canvas.bbox("all"))
    )

    canvas.create_window((0, 0), window=scroll_frame, anchor="nw")
    canvas.configure(yscrollcommand=scrollbar.set)

    container.pack(fill="both", expand=True)
    canvas.pack(side="left", fill="both", expand=True)
    scrollbar.pack(side="right", fill="y")

    # Title (reduced top padding)
    tk.Label(scroll_frame, text="Review Your Answers",
             font=('Arial', font_size_q + 4, 'bold'), bg='white', fg='black').pack(pady=(5, 10))

    # Populate review
    for i, (question, correct, options) in enumerate(self.questions):
        user_answer = self.user_answers[i] or "No Answer"
        color = "green" if user_answer == correct else "red"

        q_text = f"Q{i + 1}: {question}"
        user_text = f"Your Answer: {user_answer}"
        correct_text = f"Correct Answer: {correct}"

        tk.Label(scroll_frame, text=q_text, font=('Arial', font_size_q, 'bold'),
                 bg='white', fg='black', wraplength=screen_width - 100, justify="left").pack(anchor="w", padx=20, pady=(10, 0))
        tk.Label(scroll_frame, text=user_text, font=('Arial', font_size_a),
                 bg='white', fg=color, wraplength=screen_width - 100, justify="left").pack(anchor="w", padx=40)
        tk.Label(scroll_frame, text=correct_text, font=('Arial', font_size_a),
                 bg='white', fg='blue', wraplength=screen_width - 100, justify="left").pack(anchor="w", padx=40)

    # Back to Result button
    tk.Button(scroll_frame, text="Back to Result", font=('Arial', font_size_a + 2),
              command=self.show_result, bg='lightgray').pack(pady=30)