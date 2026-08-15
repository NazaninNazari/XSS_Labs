🧪 Lab 01 — Reflected XSS

Difficulty: 🟢 Easy

🎯 Objective
Find and demonstrate a Reflected Cross-Site Scripting (XSS) vulnerability in the search functionality.
Your goal is to make the browser execute a harmless JavaScript proof of concept.

🧩 Scenario
You are testing a simple product search application.
The application accepts a search query from the user and displays the search query on the results page.
Your task is to investigate how the user's input flows through the application and determine whether it can be interpreted as executable HTML/JavaScript.

🛠️ Technology
Python
Flask
Jinja2
HTML

🚀 Setup
1. Install the dependencies
From this directory, run:
pip install -r requirements.txt 

2. Start the application
python app.py 
The application should start at:
http://127.0.0.1:5000 

3. Open the application
Open the following address in your browser:
http://127.0.0.1:5000 

🎯 Target
The target functionality is the search parameter:
GET /?q=YOUR_INPUT 
For example:
http://127.0.0.1:5000/?q=hello 
Try different values and observe how the application handles them.

🧪 Challenge
Determine whether the q parameter can be used to execute JavaScript in the browser.
Use a harmless proof of concept for testing:
<script>alert(1)</script> 

✅ Success Condition
The Lab is successfully solved when:
alert(1) 
is executed by the browser.

🔍 Investigation
Before looking at the solution, investigate the application yourself.
Try to answer these questions:
Where does the user-controlled input enter the application?
How does Flask retrieve the input?
Where is the input sent after it is retrieved?
Where is the input reflected in the HTML?
In which HTML context is the input placed?
Is the input escaped or rendered as HTML?
Why does the browser execute the supplied JavaScript?

💡 Hints
Hint 1
Start by looking at the q parameter.
/?q=YOUR_INPUT 
Where is this value retrieved in app.py?

Hint 2
Look for:
request.args.get(...) 
What happens to the value returned by this function?

Hint 3
Follow the value into:
templates/index.html 
Find where query is rendered.

Hint 4
Think about the difference between:
Plain Text 
and:
HTML Markup 
Ask yourself:

How does the template engine decide whether user input should be treated as text or HTML?

🧠 Learning Objectives
After completing this Lab, you should understand:
What Reflected XSS is
How user-controlled input enters a web application
Source and data flow
HTML body context
Jinja2 auto-escaping
Unsafe HTML rendering
Basic XSS exploitation
Basic XSS mitigation

📌 Questions to Think About
After solving the Lab, try to explain:
1. Why is this vulnerability called Reflected XSS?
2. What is the source of the user-controlled input?
3. Where is the input reflected?
4. What is the XSS context?
5. Why does the browser execute the payload?
6. How could a developer prevent this vulnerability?

⚠️ Disclaimer
This application is intentionally vulnerable.
This Lab is designed for:
Educational purposes
Local security testing
Authorized security research
Learning web application security
Run the application locally and do not deploy this intentionally vulnerable application to a public production environment.
Only test systems that you own or have explicit permission to test.

🏁 Stuck?
Try to solve the Lab yourself before checking the solution.
The official solution is available here:
solution/README.md 
Try to identify the:
Source ↓ Data Flow ↓ Context ↓ Sink ↓ Browser Interpretation 
before opening the solution.

🚀 Next Lab
Once you understand Reflected XSS in an HTML context, move on to:
Lab 02 — Reflected XSS in HTML Attribute Context
The next Lab will introduce a different XSS context and require you to think differently about how the browser interprets user-controlled input.

---
Created by **N0aziXss**