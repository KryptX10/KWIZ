    def next_question(self):
        self.current_question_index += 1
        
        if self.current_question_index >= len(self.selected_questions):
            self.current_question_index += 1
            self.show_result()
        else:
            self.show_question()
