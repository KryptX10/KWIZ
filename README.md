def show_question(self):
    self.bg_photo = self.load_background(self.quiz_image_name)
    self.clear(exclude_timer=True)

    if self.current_question_index >= len(self.questions):
        return self.show_result()

    screen_width = self.root.winfo_screenwidth()
    screen_height = self.root.winfo_screenheight()

    q_data = self.questions[self.current_question_index]
    question_text, answer, options, image_path = q_data

    self.correct_answer = answer
    self.selected_option = None

    font_size_q = int(screen_height * 0.03)
    font_size_o = int(screen_height * 0.025)

    # Display image if exists
    if image_path:
        img = Image.open(image_path)
        img = img.resize((int(screen_width * 0.4), int(screen_height * 0.3)))
        self.image_photo = ImageTk.PhotoImage(img)
        self.image_label = tk.Label(self.canvas, image=self.image_photo, bg="white")
        self.image_label_window = self.canvas.create_window(screen_width//2, int(screen_height * 0.25), window=self.image_label)

        y_pos = int(screen_height * 0.55)
    else:
        y_pos = int(screen_height * 0.3)

    # Show question
    self.canvas.create_text(screen_width//2, y_pos, text=question_text,
                            fill="black", font=("Helvetica", font_size_q), width=int(screen_width * 0.8))

    # Shuffle options 🔀
    random.shuffle(options)

    self.option_buttons = []
    for i, option in enumerate(options):
        btn = tk.Button(self.canvas, text=option, font=("Helvetica", font_size_o),
                        width=30, bg="white", fg="black", command=lambda opt=option: self.select_option(opt))
        btn_window = self.canvas.create_window(screen_width//2, y_pos + 60 + i * 60, window=btn)
        self.option_buttons.append(btn)