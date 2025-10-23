
🧠 Claude Prompt — Smart CGPA Calculator (Flutter App)

> Task:
Build a complete Flutter mobile application called “Smart CGPA Calculator” with user authentication, local data storage, and GPA/CGPA computation features.
Use Dart and the Flutter Material 3 design system.
The app should be responsive, modern, and ready for Android deployment.




---

🏗️ App Flow & Features

1. Splash & Authentication

Show a splash screen with app name and logo.

If it’s the user’s first time:

Display a Sign Up screen (Full Name, Email/Username, Password).

Save credentials locally using shared_preferences or hive.


On future launches:

Show Login screen (Email/Username + Password).

If password matches saved data → navigate to main dashboard.




2. Dashboard

Two main options:

🧮 Add Courses

📊 View GPA/CGPA


Include an AppBar with user’s name and a settings icon.



3. Add Courses Screen

Input fields:

Course Code (text)

Course Unit (number)

Grade (dropdown: A, B, C, D, E, F)


Button: Add Course

Show a scrollable list of added courses (editable and deletable).

Save course data persistently (use Hive or SQLite).



4. GPA & CGPA Calculation

Grade points: A=5, B=4, C=3, D=2, E=1, F=0.

GPA = (Σ grade points × course units) ÷ total units for that semester.

CGPA = (Σ total grade points of all semesters) ÷ total units overall.

Allow multiple semesters — each with its own list of courses.



5. Results Screen

Display each semester’s GPA and the overall CGPA.

Use color codes:

🟢 Excellent (≥ 4.5)

🟡 Average (2.5–4.49)

🔴 Poor (< 2.5)


Include a graph or bar chart showing GPA trends (use fl_chart).



6. Settings Page

Change password option.

Toggle for Dark/Light mode.

Logout button (clears session).





---

🎨 Design Requirements

Use Material 3 components (Cards, ElevatedButtons, NavigationBar).

Rounded corners (BorderRadius.circular(16) or 20).

Soft shadows and smooth transitions (AnimatedContainer or Hero).

Modern color palette (pastel or blue-themed).

App logo at top of Sign Up/Login screens.



---

🧩 Architecture & Code Structure

lib/
 ┣ screens/
 ┃ ┣ splash_screen.dart
 ┃ ┣ signup_screen.dart
 ┃ ┣ login_screen.dart
 ┃ ┣ dashboard_screen.dart
 ┃ ┣ add_courses_screen.dart
 ┃ ┗ results_screen.dart
 ┣ models/
 ┃ ┗ course_model.dart
 ┣ services/
 ┃ ┣ auth_service.dart
 ┃ ┣ storage_service.dart
 ┃ ┗ gpa_calculator.dart
 ┣ widgets/
 ┃ ┣ course_card.dart
 ┃ ┗ custom_input_field.dart
 ┗ main.dart

Use Provider or Riverpod for state management.

Comment code clearly for readability.



---

💾 Technical Notes

Prefer Hive for local database (lightweight, key-value).

Use shared_preferences for auth info and app settings.

Store all data offline (no internet required).

Include basic validation (no empty fields, numeric units only, etc.).



---

🎁 Optional Enhancements

Export GPA/CGPA summary as PDF (using pdf package).

Display motivational messages based on GPA.

Animated welcome message with the user’s name after login.



---

🔚 Deliverables

Full Flutter source code.

Well-commented, readable Dart files.

Ready-to-build Android APK.
