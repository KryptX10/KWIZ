def view_answers(self):
    self.clear(exclude_timer=True)
    self.bg_photo = self.load_background(self.quiz_image_name)

    screen_width = self.root.winfo_screenwidth()
    screen_height = self.root.winfo_screenheight()

    font_size_q = int(screen_height * 0.025)
    font_size_a = int(screen_height * 0.02)

    # Create a new scrollable canvas and frame
    container = tk.Frame(self.root)
    container.pack(fill="both", expand=True)

    scroll_canvas = tk.Canvas(container, bg="white", highlightthickness=0)
    scrollbar = tk.Scrollbar(container, orient="vertical", command=scroll_canvas.yview)
    scroll_canvas.configure(yscrollcommand=scrollbar.set)

    scrollbar.pack(side="right", fill="y")
    scroll_canvas.pack(side="left", fill="both", expand=True)

    inner_frame = tk.Frame(scroll_canvas, bg="white")
    scroll_canvas.create_window((0, 0), window=inner_frame, anchor="nw")

    # Update scroll region when the inner_frame resizes
    def update_scroll(event):
        scroll_canvas.configure(scrollregion=scroll_canvas.bbox("all"))

    inner_frame.bind("<Configure>", update_scroll)

    # Force scroll to top in case previous scroll position is retained
    scroll_canvas.yview_moveto(0)

    # Add review content
    spacing = int(screen_height * 0.08)

    tk.Label(inner_frame, text="Review Your Answers", font=('Arial', font_size_q + 4, 'bold'),
             bg="white", fg="black").pack(pady=(5, 15))  # reduced top padding

    for i, (question, correct, options) in enumerate(self.questions):
        user_answer = self.user_answers[i] or "No Answer"
        color = "green" if user_answer == correct else "red"

        q_text = f"Q{i + 1}: {question}"
        user_text = f"Your Answer: {user_answer}"
        correct_text = f"Correct Answer: {correct}"

        tk.Label(inner_frame, text=q_text, font=('Arial', font_size_q, 'bold'), bg="white",
                 wraplength=screen_width - 100, justify="left").pack(anchor="w", padx=20, pady=(0, 2))
        tk.Label(inner_frame, text=user_text, font=('Arial', font_size_a), fg=color,
                 bg="white", wraplength=screen_width - 100, justify="left").pack(anchor="w", padx=40)
        tk.Label(inner_frame, text=correct_text, font=('Arial', font_size_a), fg="blue",
                 bg="white", wraplength=screen_width - 100, justify="left").pack(anchor="w", padx=40)
        tk.Label(inner_frame, text="", bg="white").pack(pady=spacing // 4)  # Spacer

    # Back to results button
    tk.Button(inner_frame, text="Back to Result", font=('Arial', font_size_a + 2),
              command=self.show_result, bg="lightgray").pack(pady=20)