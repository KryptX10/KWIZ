def view_answers(self):
    self.clear(exclude_timer=True)
    screen_width = self.root.winfo_screenwidth()
    screen_height = self.root.winfo_screenheight()

    font_size_q = int(screen_height * 0.025)
    font_size_a = int(screen_height * 0.02)
    spacing = int(screen_height * 0.08)

    # Create scrollable frame
    container = tk.Frame(self.root, bg='white')
    canvas = tk.Canvas(container, bg='white', width=screen_width, height=screen_height)
    scrollbar = tk.Scrollbar(container, orient="vertical", command=canvas.yview)
    scrollable_frame = tk.Frame(canvas, bg='white')

    scrollable_frame.bind(
        "<Configure>",
        lambda e: canvas.configure(scrollregion=canvas.bbox("all"))
    )

    canvas.create_window((0, 0), window=scrollable_frame, anchor="nw")
    canvas.configure(yscrollcommand=scrollbar.set)

    # Place container using canvas so it's aligned with your window
    self.canvas.create_window(screen_width // 2, screen_height // 2, window=container, anchor="center")
    container.pack(fill='both', expand=True)
    canvas.pack(side="left", fill="both", expand=True)
    scrollbar.pack(side="right", fill="y")

    # Title
    tk.Label(scrollable_frame, text="Review Your Answers", font=('Arial', font_size_q + 4, 'bold'),
             fg='black', bg='white').pack(pady=10)

    # Display Q&A
    for i, (question, correct, options) in enumerate(self.questions):
        user_answer = self.user_answers[i] or "No Answer"
        color = "green" if user_answer == correct else "red"

        q_text = f"Q{i + 1}: {question}"
        user_text = f"Your Answer: {user_answer}"
        correct_text = f"Correct Answer: {correct}"

        tk.Label(scrollable_frame, text=q_text, font=('Arial', font_size_q, 'bold'),
                 wraplength=screen_width - 100, justify='left', bg='white').pack(anchor='w', padx=30)
        tk.Label(scrollable_frame, text=user_text, font=('Arial', font_size_a),
                 fg=color, bg='white').pack(anchor='w', padx=50)
        tk.Label(scrollable_frame, text=correct_text, font=('Arial', font_size_a),
                 fg='blue', bg='white').pack(anchor='w', padx=50)
        tk.Label(scrollable_frame, text="", bg='white').pack(pady=spacing // 4)  # spacer

    # Back Button
    tk.Button(scrollable_frame, text="Back to Result", font=('Arial', font_size_a + 2),
              command=self.show_result).pack(pady=30)