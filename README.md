    def select_option(opt, btn):
        for b in self.option_buttons:
            b.config(bg='white', relief='raised')
            btn.config(bg='lightblue', relief='sunken')
            self.selected_option = opt
            
            for i, opt in enumerate(options):
                btn = tk.Button(self.root, text=opt,
                            font=('Arial', font_size_options), width=40,
                            anchor='w', justify='left', bg='white', relief='raised')
                btn.config(command=lambda o=opt, b=btn: select_option(o, b))
                self.option_buttons.append(btn)
                self.canvas.create_window(screen_width // 2, option_start_y + i * option_spacing, window=btn)
                
                previous_selection = self.user_answers[self.current_question_index]
                if previous_selection:
                    for btn in self.option_buttons:
                        if btn['text'] == previous_selection:
                            btn.config(bg='lightblue', relief='sunken')
                            self.selected_option = previous_selection
                            break
                            btn_frame = tk.Frame(self.root, bg='white')
                            if self.current_question_index > 0:
            prev_btn = tk.Button(btn_frame, text="Previous", font=('Arial', 14), command=self.prev_question)
            prev_btn.pack(side='left', padx=10)

        next_btn = tk.Button(btn_frame, text="Next", font=('Arial', 14), command=self.next_question)
        next_btn.pack(side='left', padx=10)

        self.canvas.create_window(screen_width // 2, option_start_y + len(options) * option_spacing + 30,
                                  window=btn_frame)

        self.update_timer()  # ✅ Ensure timer is shown on every question
