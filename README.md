def view_answers(self):
    self.clear()
    self.bg_photo = self.load_background(self.quiz_image_name)

    screen_width = self.root.winfo_screenwidth()
    screen_height = self.root.winfo_screenheight()

    font_size_q = int(screen_height * 0.025)
    font_size_a = int(screen_height * 0.022)

    # Create scrollable canvas
    frame_canvas = tk.Frame(self.root)
    frame_canvas.pack(fill=tk.BOTH, expand=1)

    canvas = tk.Canvas(frame_canvas, bg='white')
    canvas.pack(side=tk.LEFT, fill=tk.BOTH, expand=1)

    scrollbar = tk.Scrollbar(frame_canvas, orient=tk.VERTICAL, command=canvas.yview)
    scrollbar.pack(side=tk.RIGHT, fill=tk.Y)

    canvas.configure(yscrollcommand=scrollbar.set)
    canvas.bind('<Configure>', lambda e: canvas.configure(scrollregion=canvas.bbox("all")))

    answer_frame = tk.Frame(canvas, bg='white')
    canvas.create_window((0, 0), window=answer_frame, anchor="nw", width=screen_width)

    # Add answers
    for i, (q, correct_ans, options) in enumerate(self.questions):
        user_ans = self.user_answers[i]

        q_label = tk.Label(answer_frame, text=f"Q{i+1}: {q}", font=('Arial', font_size_q, 'bold'),
                           bg='white', anchor='w', justify='left', wraplength=screen_width - 100)
        q_label.pack(padx=20, pady=(10, 0), anchor='w')

        ans_text = f"Your answer: {user_ans or 'None'}    |    Correct answer: {correct_ans}"
        color = 'green' if user_ans == correct_ans else 'red'

        a_label = tk.Label(answer_frame, text=ans_text, font=('Arial', font_size_a),
                           fg=color, bg='white', anchor='w', justify='left')
        a_label.pack(padx=40, anchor='w')

    # Back button
    back_btn = tk.Button(self.root, text="Back to Result", font=('Arial', 14), command=self.show_result)
    self.canvas.create_window(screen_width // 2, screen_height - 40, window=back_btn)