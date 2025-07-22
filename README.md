def next_question(self):
    self.current_question_index += 1
    
    if self.current_question_index >= len(self.selected_questions):
        self.show_result()  # ✅ Only call the result function here
    else:
        self.show_question()  # ✅ Otherwise show the next question