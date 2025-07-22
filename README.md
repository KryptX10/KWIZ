import tkinter as tk
from tkinter import messagebox
from PIL import Image, ImageTk
import random
import sys
import os

from PIL.ImageChops import screen


# ========== FOR PACKAGING SUPPORT ==========
def resource_path(relative_path):
    """ Get absolute path to resource, works for development and PyInstaller """
    try:
        base_path = sys._MEIPASS  # set by PyInstaller
    except Exception:
        base_path = os.path.abspath(".")
    return os.path.join(base_path, relative_path)


# ========== QUESTIONS DATABASE ==========
questions_data = {
"Math": [
        ("What is 2 + 2?", "4", ["3", "4", "5", "6"]),
        ("What is 5 * 3?", "15", ["10", "15", "20", "25"]),
        ("What is 10 / 2?", "5", ["4", "5", "6", "7"]),
        ("What is 7 - 3?", "4", ["3", "4", "5", "6"]),
        ("What is 3 squared?", "9", ["6", "7", "8", "9"]),
        ("What is the square root of 16?", "4", ["4", "5", "6", "7"]),
        ("What is 8 + 5?", "13", ["13", "14", "15", "16"]),
        ("What is 12 - 4?", "8", ["6", "7", "8", "9"]),
        ("What is 9 * 1?", "9", ["8", "9", "10", "11"]),
        ("What is 6 / 2?", "3", ["2", "3", "4", "5"]),
        ("What is 11 + 2?", "13", ["12", "13", "14", "15"]),
        ("What is 14 - 7?", "7", ["6", "7", "8", "9"]),
        ("What is 3 * 4?", "12", ["10", "11", "12", "13"]),
        ("What is 25 / 5?", "5", ["4", "5", "6", "7"]),
        ("What is 1 + 9?", "10", ["9", "10", "11", "12"]),
        ("What is 7 + 6?", "13", ["12", "13", "14", "15"]),
        ("What is 5 + 5?", "10", ["10", "11", "12", "13"]),
        ("What is 8 - 3?", "5", ["4", "5", "6", "7"]),
        ("What is 2 * 6?", "12", ["10", "11", "12", "13"]),
        ("What is 16 / 4?", "4", ["3", "4", "5", "6"]),
    ],
    "Science": [
        ("What planet is known as the Red Planet?", "Mars", ["Mars", "Earth", "Venus", "Jupiter"]),
        ("What is H2O?", "Water", ["Water", "Oxygen", "Hydrogen", "Carbon"]),
        ("What gas do plants absorb?", "Carbon dioxide", ["Oxygen", "Hydrogen", "Carbon dioxide", "Nitrogen"]),
        ("Which organ pumps blood?", "Heart", ["Heart", "Lungs", "Liver", "Kidney"]),
        ("What force pulls objects down?", "Gravity", ["Magnetism", "Friction", "Gravity", "Electricity"]),
        ("What is the boiling point of water?", "100", ["90", "100", "80", "110"]),
        ("Which planet is closest to the Sun?", "Mercury", ["Venus", "Mercury", "Earth", "Mars"]),
        ("What gas do humans exhale?", "Carbon dioxide", ["Oxygen", "Hydrogen", "Carbon dioxide", "Nitrogen"]),
        ("What part of the plant makes food?", "Leaves", ["Roots", "Stems", "Leaves", "Flowers"]),
        ("What do bees make?", "Honey", ["Milk", "Honey", "Nectar", "Juice"]),
        ("Which planet is known for rings?", "Saturn", ["Saturn", "Jupiter", "Mars", "Neptune"]),
        ("What do fish use to breathe?", "Gills", ["Gills", "Lungs", "Mouth", "Skin"]),
        ("What causes tides?", "Moon", ["Sun", "Moon", "Earth", "Wind"]),
        ("Which organ helps in digestion?", "Stomach", ["Heart", "Lungs", "Stomach", "Kidney"]),
        ("What is frozen water called?", "Ice", ["Ice", "Vapor", "Snow", "Rain"]),
        ("Which is the hottest planet?", "Venus", ["Mercury", "Earth", "Venus", "Mars"]),
        ("Which part of the body helps us see?", "Eyes", ["Ears", "Nose", "Eyes", "Mouth"]),
        ("Which energy comes from the sun?", "Solar", ["Wind", "Hydro", "Nuclear", "Solar"]),
        ("What is the Earth's only satellite?", "Moon", ["Sun", "Mars", "Moon", "Venus"]),
        ("Which gas is essential for breathing?", "Oxygen", ["Carbon", "Hydrogen", "Oxygen", "Nitrogen"]),
    ],
    "History": [
        ("Who was the first President of the USA?", "George Washington", ["George Washington", "Lincoln", "Adams", "Jefferson"]),
        ("In what year did WWII end?", "1945", ["1945", "1940", "1939", "1950"]),
        ("Who discovered America?", "Christopher Columbus", ["Christopher Columbus", "Magellan", "Cook", "Drake"]),
        ("Which country built the Great Wall?", "China", ["China", "India", "Egypt", "Greece"]),
        ("Who was known as Iron Lady?", "Margaret Thatcher", ["Indira Gandhi", "Margaret Thatcher", "Angela Merkel", "Golda Meir"]),
        ("What ship sank in 1912?", "Titanic", ["Titanic", "Lusitania", "Bismarck", "Endeavour"]),
        ("Who was Abraham Lincoln?", "US President", ["Inventor", "Scientist", "US President", "General"]),
        ("Who painted the Mona Lisa?", "Leonardo da Vinci", ["Leonardo da Vinci", "Michelangelo", "Raphael", "Picasso"]),
        ("Where did Napoleon come from?", "France", ["Italy", "Germany", "France", "Spain"]),
        ("When did Berlin Wall fall?", "1989", ["1987", "1989", "1991", "1995"]),
        ("Who ruled the Mongol Empire?", "Genghis Khan", ["Genghis Khan", "Attila", "Alexander", "Julius Caesar"]),
        ("Which empire built pyramids?", "Egyptian", ["Greek", "Roman", "Egyptian", "Persian"]),
        ("What was WWI also called?", "The Great War", ["Cold War", "The Great War", "War of Nations", "World Conflict"]),
        ("Where was the first democracy?", "Athens", ["Rome", "Athens", "Sparta", "Babylon"]),
        ("Who abolished slavery in USA?", "Abraham Lincoln", ["George Washington", "Abraham Lincoln", "John Adams", "FDR"]),
        ("Who was Julius Caesar?", "Roman general", ["Greek Philosopher", "Roman general", "Egyptian King", "Persian Emperor"]),
        ("When was the UN founded?", "1945", ["1919", "1939", "1945", "1950"]),
        ("Which war lasted 100 years?", "Hundred Years War", ["Hundred Years War", "WWII", "Napoleonic War", "Civil War"]),
        ("What is Stonehenge?", "Prehistoric monument", ["Temple", "Prehistoric monument", "Fort", "Tomb"]),
        ("Who was Nelson Mandela?", "South African Leader", ["US President", "Nobel Scientist", "South African Leader", "British PM"]),
    ],
    "English": [
        ("What is a synonym of 'Happy'?", "Glad", ["Sad", "Angry", "Glad", "Mad"]),
        ("What is the opposite of 'Cold'?", "Hot", ["Warm", "Cool", "Hot", "Ice"]),
        ("What is a noun?", "Name of person/place", ["Describes action", "Name of person/place", "Joins sentences", "Describes noun"]),
        ("What is a verb?", "Action word", ["Action word", "Name", "Describes noun", "Pronoun"]),
        ("What is a pronoun?", "Replaces noun", ["Describes verb", "Replaces noun", "Links phrases", "Describes noun"]),
        ("Which is a punctuation mark?", "!", [".", ",", "!", "a"]),
        ("Which word is an adjective?", "Beautiful", ["Run", "Beautiful", "Quickly", "She"]),
        ("What is the plural of 'Child'?", "Children", ["Childs", "Childes", "Children", "Childer"]),
        ("'He is tall' - what is 'He'?", "Pronoun", ["Noun", "Adjective", "Pronoun", "Verb"]),
        ("Past tense of 'Go'?", "Went", ["Go", "Goes", "Gone", "Went"]),
        ("Which is a question word?", "Why", ["It", "Why", "And", "With"]),
        ("Antonym of 'Dark'?", "Bright", ["Black", "Bright", "Dim", "Light"]),
        ("What is a conjunction?", "Joins words", ["Ends sentence", "Joins words", "Describes action", "Pronoun"]),
        ("Which sentence is correct?", "She is kind.", ["She kind is.", "She is kind.", "Kind is she.", "Is she kind"]),
        ("Word that shows emotion?", "Interjection", ["Verb", "Adjective", "Interjection", "Noun"]),
        ("Comparative of 'Big'?", "Bigger", ["Big", "Bigger", "Biggest", "More Big"]),
        ("Which is a homophone?", "Two", ["To", "Too", "Two", "Twu"]),
        ("What do we use for names?", "Capital letter", ["Small letter", "Symbol", "Capital letter", "Underline"]),
        ("What is a sentence?", "Complete idea", ["Word", "Verb", "Noun", "Complete idea"]),
        ("Plural of 'Mouse'?", "Mice", ["Mouses", "Mouse", "Mice", "Mousez"]),
    ],
"Biology": [
    ("What is the powerhouse of the cell?", "Mitochondria", ["Nucleus", "Ribosome", "Mitochondria", "Chloroplast"]),
    ("What carries genetic information?", "DNA", ["RNA", "Protein", "DNA", "Glucose"]),
    ("What organ pumps blood?", "Heart", ["Brain", "Liver", "Heart", "Kidney"]),
    ("Which organ helps with breathing?", "Lungs", ["Heart", "Stomach", "Lungs", "Liver"]),
    ("What do plants use for photosynthesis?", "Chlorophyll", ["Carbon", "Water", "Chlorophyll", "Glucose"]),
    ("What is the basic unit of life?", "Cell", ["Tissue", "Organ", "Cell", "System"]),
    ("What is the function of red blood cells?", "Carry oxygen", ["Fight infection", "Carry oxygen", "Digest food", "Send signals"]),
    ("Which part of the plant absorbs water?", "Root", ["Leaf", "Stem", "Root", "Flower"]),
    ("Which human organ filters blood?", "Kidney", ["Heart", "Lung", "Kidney", "Liver"]),
    ("What is the process of shedding skin?", "Moulting", ["Shedding", "Peeling", "Moulting", "Layering"]),
    ("What type of blood cells fight disease?", "White blood cells", ["Red blood cells", "White blood cells", "Platelets", "Plasma"]),
    ("Which vitamin is from sunlight?", "Vitamin D", ["Vitamin A", "Vitamin B", "Vitamin D", "Vitamin C"]),
    ("What is the largest organ of the human body?", "Skin", ["Liver", "Heart", "Skin", "Lung"]),
    ("What gas do humans inhale?", "Oxygen", ["Carbon dioxide", "Oxygen", "Nitrogen", "Hydrogen"]),
    ("Which part of the brain controls balance?", "Cerebellum", ["Cerebrum", "Cerebellum", "Medulla", "Thalamus"]),
    ("What is an animal that eats both plants and animals?", "Omnivore", ["Carnivore", "Herbivore", "Omnivore", "Detritivore"]),
    ("Which system controls movement?", "Muscular system", ["Respiratory system", "Muscular system", "Digestive system", "Circulatory system"]),
    ("What do enzymes do?", "Speed up reactions", ["Store energy", "Break bones", "Speed up reactions", "Carry oxygen"]),
    ("What is another name for windpipe?", "Trachea", ["Bronchi", "Esophagus", "Trachea", "Nostrils"]),
    ("Which organ produces insulin?", "Pancreas", ["Liver", "Pancreas", "Kidney", "Stomach"]),
],
"Computer": [
    ("What does CPU stand for?", "Central Processing Unit", ["Central Processing Unit", "Computer Personal Unit", "Central Program Utility", "Control Processing Unit"]),
    ("What is the brain of the computer?", "CPU", ["CPU", "RAM", "Monitor", "Hard Disk"]),
    ("Which device is used for typing?", "Keyboard", ["Mouse", "Monitor", "Keyboard", "Scanner"]),
    ("Which device is an output device?", "Printer", ["Keyboard", "Scanner", "Printer", "Mouse"]),
    ("What does RAM stand for?", "Random Access Memory", ["Read Access Memory", "Random Access Memory", "Ready Access Mode", "Run Access Mode"]),
    ("What is the full form of USB?", "Universal Serial Bus", ["Universal Serial Bus", "United System Board", "User Service Bus", "Universal Service Base"]),
    ("Which software is used for writing letters?", "Word Processor", ["Spreadsheet", "Word Processor", "Database", "Presentation"]),
    ("Which key is used to delete text?", "Backspace", ["Enter", "Shift", "Backspace", "Ctrl"]),
    ("Which part stores data permanently?", "Hard Disk", ["RAM", "ROM", "Hard Disk", "Cache"]),
    ("What is used to surf the Internet?", "Web Browser", ["Web Server", "Word Processor", "Web Browser", "Compiler"]),
    ("Which is an input device?", "Scanner", ["Monitor", "Printer", "Scanner", "Speaker"]),
    ("What does WWW stand for?", "World Wide Web", ["World Web Window", "World Wide Web", "Wide Web World", "Web World Wide"]),
    ("What is a virus in computing?", "Malicious software", ["Programming language", "Useful tool", "Malicious software", "Hardware failure"]),
    ("Which of these is a programming language?", "Python", ["Windows", "Linux", "Python", "Excel"]),
    ("What is used to store large data?", "Database", ["Processor", "Database", "Spreadsheet", "Compiler"]),
    ("Which one is a search engine?", "Google", ["Windows", "Google", "Excel", "Facebook"]),
    ("What is the main screen of a computer called?", "Desktop", ["Monitor", "Window", "Desktop", "Screen"]),
    ("What is the unit of data storage?", "Byte", ["Bit", "Byte", "Pixel", "Click"]),
    ("What does GUI stand for?", "Graphical User Interface", ["Graphical User Interface", "General User Input", "Global Unit Info", "Graph Use Interface"]),
    ("What type of file is '.exe'?", "Executable", ["Document", "Image", "Executable", "Compressed"]),
],
"Chemistry": [
    ("What is the chemical symbol for Gold?", "Au", ["Au", "Ag", "Gd", "Go"]),
    ("What is H2O?", "Water", ["Hydrogen", "Oxygen", "Water", "Carbon"]),
    ("What is NaCl?", "Salt", ["Salt", "Sugar", "Chlorine", "Sodium"]),
    ("What is the pH of a neutral substance?", "7", ["0", "7", "14", "10"]),
    ("Which gas is used for breathing?", "Oxygen", ["Oxygen", "Nitrogen", "Carbon", "Hydrogen"]),
    ("What is the chemical symbol for Iron?", "Fe", ["Fe", "Ir", "In", "I"]),
    ("What is the state of water at 100°C?", "Gas", ["Liquid", "Gas", "Solid", "Ice"]),
    ("Which element is a noble gas?", "Neon", ["Neon", "Oxygen", "Nitrogen", "Hydrogen"]),
    ("What is the chemical formula for carbon dioxide?", "CO2", ["CO2", "C2O", "OC2", "C2"]),
    ("What type of bond is in NaCl?", "Ionic", ["Covalent", "Ionic", "Metallic", "Hydrogen"]),
    ("Which acid is in lemon?", "Citric acid", ["Citric acid", "Acetic acid", "Sulfuric acid", "Hydrochloric acid"]),
    ("Which metal is liquid at room temperature?", "Mercury", ["Mercury", "Iron", "Aluminum", "Zinc"]),
    ("Which gas is used in balloons?", "Helium", ["Oxygen", "Hydrogen", "Nitrogen", "Helium"]),
    ("What is CH4?", "Methane", ["Methane", "Ethane", "Propane", "Butane"]),
    ("What is the chemical symbol for Potassium?", "K", ["P", "K", "Po", "Pt"]),
    ("Which element has atomic number 1?", "Hydrogen", ["Hydrogen", "Helium", "Oxygen", "Carbon"]),
    ("What is the most abundant gas in air?", "Nitrogen", ["Oxygen", "Carbon", "Nitrogen", "Hydrogen"]),
    ("What is vinegar mostly made of?", "Acetic acid", ["Citric acid", "Acetic acid", "Sulfuric acid", "Lactic acid"]),
    ("Which element is used in light bulbs?", "Tungsten", ["Tungsten", "Iron", "Zinc", "Silver"]),
    ("Which gas turns limewater milky?", "Carbon dioxide", ["Oxygen", "Hydrogen", "Carbon dioxide", "Nitrogen"]),
],
#Code for Geography
"Geography": [
    ("What is the capital of France?", "Paris", ["Paris", "Rome", "London", "Berlin"]),
    ("Which continent is Egypt in?", "Africa", ["Asia", "Africa", "Europe", "Australia"]),
    ("Which ocean is the largest?", "Pacific", ["Atlantic", "Indian", "Arctic", "Pacific"]),
    ("What is the longest river in the world?", "Nile", ["Amazon", "Nile", "Yangtze", "Mississippi"]),
    ("What is the capital of Nigeria?", "Abuja", ["Lagos", "Kano", "Abuja", "Kaduna"]),
    ("Which is the coldest continent?", "Antarctica", ["Europe", "Asia", "Antarctica", "America"]),
    ("Which country has the most population?", "China", ["India", "USA", "China", "Russia"]),
    ("What is the capital of Japan?", "Tokyo", ["Kyoto", "Tokyo", "Osaka", "Seoul"]),
    ("Which country is shaped like a boot?", "Italy", ["Spain", "Italy", "France", "Germany"]),
    ("Which desert is the largest?", "Sahara", ["Gobi", "Kalahari", "Sahara", "Thar"]),
    ("What is the smallest continent?", "Australia", ["Europe", "Australia", "Antarctica", "South America"]),
    ("What is the capital of Canada?", "Ottawa", ["Toronto", "Montreal", "Ottawa", "Vancouver"]),
    ("Which line divides Earth into hemispheres?", "Equator", ["Prime Meridian", "Equator", "Tropic", "Axis"]),
    ("Which mountain is the highest?", "Everest", ["Kilimanjaro", "Everest", "Alps", "Andes"]),
    ("What is the capital of Kenya?", "Nairobi", ["Mombasa", "Nairobi", "Kampala", "Dar es Salaam"]),
    ("Which US state is an island?", "Hawaii", ["California", "Alaska", "Hawaii", "Florida"]),
    ("Which country is known for pyramids?", "Egypt", ["Mexico", "Greece", "Egypt", "India"]),
    ("What is the capital of Brazil?", "Brasília", ["Rio", "São Paulo", "Brasília", "Salvador"]),
    ("What separates Europe from Asia?", "Ural Mountains", ["Andes", "Alps", "Ural Mountains", "Himalayas"]),
    ("Which continent has the most countries?", "Africa", ["Asia", "Europe", "Africa", "America"]),
],
}

class QuizApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Quiz App")
        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()
        self.root.geometry(f"{screen_width}x{screen_height}")
        self.root.iconbitmap(resource_path("applogo.ico"))
        self.cover_image_name = "coverImage.png"
        self.quiz_image_name = "bgimage.png"
        self.bg_photo = self.load_background(self.cover_image_name)

        self.canvas = tk.Canvas(self.root, width=500, height=450)
        self.canvas.pack(fill="both", expand=True)
        self.bg = self.canvas.create_image(0, 0, anchor="nw", image=self.bg_photo)

        self.exam_duration = 600
        self.name_screen()

    def load_background(self, image_name):
        try:
            screen_width = self.root.winfo_screenwidth()
            screen_height = self.root.winfo_screenheight()
            bg_image = Image.open(resource_path(image_name))
            bg_image = bg_image.resize((screen_width,screen_height)) #resize to fit window
            return ImageTk.PhotoImage(bg_image)
        except Exception as e:
            messagebox.showerror("Image Error", f"Failed to load '{image_name}'.\nError: {e}")
            self.root.destroy()

    def clear(self, exclude_timer=False):
        for widget in self.root.winfo_children():
            if widget != self.canvas and (not exclude_timer or widget != getattr(self, 'timer_label', None)):
                widget.destroy()
        self.canvas.delete("all")
        self.bg = self.canvas.create_image(0, 0, anchor="nw", image=self.bg_photo)

    def name_screen(self):
        self.bg_photo = self.load_background(self.cover_image_name)
        self.clear()
        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()
        self.name = ""
        self.selected_subjects = []
        self.current_question_index = 0
        self.score = 0
        self.questions = []
        self.remaining_time = self.exam_duration

        self.name_label = tk.Label(self.root, text="Enter your name:", font=('Arial', 20), bg='white')
        self.name_entry = tk.Entry(self.root, font=('Arial', 18), width=30)
        self.next_button = tk.Button(self.root, text="Next", font=('Arial', 14), command=self.select_subjects)

        self.canvas.create_window(screen_width // 2, screen_height // 3, window=self.name_label)
        self.canvas.create_window(screen_width // 2, screen_height // 3 + 50, window=self.name_entry)
        self.canvas.create_window(screen_width // 2, screen_height // 3 + 100, window=self.next_button)

    def select_subjects(self):
        self.name = self.name_entry.get().strip()
        if not self.name:
            messagebox.showerror("Input Error", "Please enter your name.")
            return

        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear()

        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()

        self.canvas.create_text(screen_width // 2, 50, text="Select 4 subjects:", font=('Arial', 20), fill='black')

        self.selected_subjects = []
        self.subject_buttons = {}
        subjects = list(questions_data.keys())

        cols = 4
        spacing_x = screen_width // (cols + 1)
        spacing_y = screen_height // 4

        for index, subject in enumerate(subjects):
            row = index // cols
            col = index % cols
            x = spacing_x * (col + 1)
            y = 100 + row * 80

            btn = tk.Button(
                self.root, text=subject, font=('Arial', 14), width=15, bg='white', relief='raised',
                command=lambda s=subject: self.toggle_subject(s)
            )
            self.canvas.create_window(x, y, window=btn)
            self.subject_buttons[subject] = btn

        start_btn = tk.Button(self.root, text="Start Quiz", font=('Arial', 14), command=self.start_quiz)
        self.canvas.create_window(screen_width // 2, screen_height - 100, window=start_btn)

    def toggle_subject(self, subject):
        if subject in self.selected_subjects:
            self.selected_subjects.remove(subject)
            self.subject_buttons[subject].config(bg='white', relief='raised')
        else:
            if len(self.selected_subjects) >= 4:
                messagebox.showwarning("Limit Reached", "You can only select 4 subjects.")
                return
            self.selected_subjects.append(subject)
            self.subject_buttons[subject].config(bg='lightblue', relief='sunken')

    def start_quiz(self):
        if len(self.selected_subjects) != 4:
            messagebox.showerror("Selection Error", "Please select exactly 4 subjects.")
            return

        self.questions = []
        for subj in self.selected_subjects:
            subject_questions = questions_data[subj]
            if len(subject_questions) < 10:
                messagebox.showerror("Data Error", f"Not enough questions in {subj}.")
                return
            self.questions += random.sample(subject_questions, 10)

        random.shuffle(self.questions)
        self.current_question_index = 0
        self.score = 0
        self.start_timer()
        self.show_question()

    def start_timer(self):
        self.update_timer()
        self.timer = self.root.after(1000, self.decrement_timer)

    def update_timer(self):
        mins = self.remaining_time // 60
        secs = self.remaining_time % 60
        timer_text = f"Time Left: {mins:02d}:{secs:02d}"
        if hasattr(self, "timer_label"):
            self.timer_label.config(text=timer_text)
        else:
            self.timer_label = tk.Label(self.root, text=timer_text, font=('Arial', 12), fg='red', bg='white')
            self.canvas.create_window(400, 20, window=self.timer_label)

    def decrement_timer(self):
        self.remaining_time -= 1
        self.update_timer()
        if self.remaining_time <= 0:
            self.show_result()
        else:
            self.timer = self.root.after(1000, self.decrement_timer)

    def show_question(self):
        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear(exclude_timer=True)

        if self.current_question_index >= len(self.questions):
            return self.show_result()

        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()

        q, ans, options = self.questions[self.current_question_index]
        self.correct_answer = ans
        self.selected_option = None
        self.option_buttons = []

        font_size_question = int(screen_height * 0.03)
        font_size_options = int(screen_height * 0.025)

        self.canvas.create_text(screen_width // 2, screen_height * 0.05,
                                text=f"Question {self.current_question_index + 1} of {len(self.questions)}",
                                font=('Arial', font_size_question, 'bold'), fill='black')

        self.canvas.create_text(screen_width // 2, screen_height * 0.12, text=q,
                                font=('Arial', font_size_question, 'bold'),
                                fill='black', width=int(screen_width * 0.8))

        option_start_y = int(screen_height * 0.18)
        option_spacing = int(screen_height * 0.06)

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

        btn_frame = tk.Frame(self.root, bg='white')
        if self.current_question_index > 0:
            prev_btn = tk.Button(btn_frame, text="Previous", font=('Arial', 14), command=self.prev_question)
            prev_btn.pack(side='left', padx=10)

        next_btn = tk.Button(btn_frame, text="Next", font=('Arial', 14), command=self.next_question)
        next_btn.pack(side='left', padx=10)

        self.canvas.create_window(screen_width // 2, option_start_y + len(options) * option_spacing + 30,
                                  window=btn_frame)

    def select_subjects(self):
        self.name = self.name_entry.get().strip()
        if not self.name:
            messagebox.showerror("Input Error", "Please enter your name.")
            return

        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear()

        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()

        self.canvas.create_text(screen_width // 2, 50, text="Select 4 subjects:", font=('Arial', 20), fill='black')

        self.selected_subjects = []
        self.subject_buttons = {}
        subjects = list(questions_data.keys())

        cols = 4
        spacing_x = screen_width // (cols + 1)
        spacing_y = screen_height // 4

        for index, subject in enumerate(subjects):
            row = index // cols
            col = index % cols
            x = spacing_x * (col + 1)
            y = 100 + row * 80

            btn = tk.Button(
                self.root, text=subject, font=('Arial', 14), width=15, bg='white', relief='raised',
                command=lambda s=subject: self.toggle_subject(s)
            )
            self.canvas.create_window(x, y, window=btn)
            self.subject_buttons[subject] = btn

        start_btn = tk.Button(self.root, text="Start Quiz", font=('Arial', 14), command=self.start_quiz)
        self.canvas.create_window(screen_width // 2, screen_height - 100, window=start_btn)

    def toggle_subject(self, subject):
        if subject in self.selected_subjects:
            self.selected_subjects.remove(subject)
            self.subject_buttons[subject].config(bg='white', relief='raised')
        else:
            if len(self.selected_subjects) >= 4:
                messagebox.showwarning("Limit Reached", "You can only select 4 subjects.")
                return
            self.selected_subjects.append(subject)
            self.subject_buttons[subject].config(bg='lightblue', relief='sunken')

    def start_quiz(self):
        if len(self.selected_subjects) != 4:
            messagebox.showerror("Selection Error", "Please select exactly 4 subjects.")
            return

        self.questions = []
        for subj in self.selected_subjects:
            subject_questions = questions_data[subj]
            if len(subject_questions) < 10:
                messagebox.showerror("Data Error", f"Not enough questions in {subj}.")
                return
            self.questions += random.sample(subject_questions, 10)

        random.shuffle(self.questions)
        self.current_question_index = 0
        self.score = 0
        self.start_timer()
        self.show_question()

    def next_question(self):
        if self.selected_option.get() == self.correct_answer:
            self.score += 1
        self.current_question_index += 1
        self.show_question()

    def prev_question(self):
        if self.current_question_index > 0:
            self.current_question_index -= 1
            self.show_question()

    def show_result(self):
        self.root.after_cancel(self.timer)
        self.bg_photo = self.load_background(self.quiz_image_name)
        self.clear()
        result_text = f"{self.name}, your score is {self.score} out of {len(self.questions)}"
        self.canvas.create_text(250, 150, text=result_text, font=('Arial', 16), fill='black')
        restart_btn = tk.Button(self.root, text="Restart", command=self.name_screen)
        self.canvas.create_window(250, 200, window=restart_btn)

# Run app
if __name__ == "__main__":
    root = tk.Tk()
    app = QuizApp(root)
    root.mainloop()
