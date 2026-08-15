🧪 Lab 01 — Reflected XSS — Solution

🎯 Vulnerability
This Lab contains a Reflected Cross-Site Scripting (XSS) vulnerability in the search functionality.
The vulnerability occurs because user-controlled input is rendered as trusted HTML instead of being safely escaped.

1. Identify the Source
The application receives the q parameter from the HTTP request.
In app.py:
query = request.args.get("q", "") 
The user controls the value of q.
For example:
http://127.0.0.1:5000/?q=hello 
The data flow starts here:
User Input ↓ ?q=hello ↓ request.args.get("q") ↓ query 
Therefore:
Source: q URL parameter.

2. Follow the Data Flow
After retrieving the value, the application passes it to the Jinja2 template:
return render_template("index.html", query=query) 
The complete server-side flow is:
HTTP Request ↓ ?q=USER_INPUT ↓ request.args.get("q") ↓ query ↓ render_template() ↓ index.html 

3. Identify the Vulnerable Code
The input is rendered in templates/index.html:
{% if query %} <h2> Search results for: {{ query | safe }} </h2> {% endif %} 
The important part is:
{{ query | safe }} 

4. Understand safe
Jinja2 normally escapes HTML characters when rendering variables.
For example, HTML supplied as user input should normally be treated as text rather than markup.
However, the safe filter tells Jinja2 to treat the value as trusted HTML.
In this intentionally vulnerable Lab:
User Input ↓ {{ query | safe }} ↓ Trusted HTML 
This means attacker-controlled HTML can become part of the rendered page.

5. Identify the XSS Context
The value is inserted between HTML tags:
<h2> USER_INPUT </h2> 
Therefore the input is in:
HTML Body Context 
This is important because XSS behavior depends heavily on the context in which user-controlled data is interpreted.

6. Proof of Concept
A harmless proof of concept is:
<script>alert(1)</script> 
When supplied through the q parameter:
http://127.0.0.1:5000/?q=... 
the application reflects the input into the HTML response.
Because the value is rendered as trusted HTML, the browser interprets the <script> element as markup.
The JavaScript then executes:
alert(1) 

7. Why Is This Reflected XSS?
The vulnerability follows this sequence:
User ↓ HTTP Request ↓ q parameter ↓ Flask ↓ Jinja2 ↓ HTTP Response ↓ Browser ↓ JavaScript Execution 
The input is reflected from the request into the response.
It is not stored in a database.
Therefore, the vulnerability is classified as:
Reflected XSS 

8. Root Cause
The root cause is unsafe rendering of untrusted user input as HTML.
The application uses:
{{ query | safe }} 
This disables the normal escaping behavior for this value.
The application is effectively treating attacker-controlled data as trusted HTML.

9. Secure Fix
The safe filter should not be used for this untrusted value.

❌ Vulnerable
{{ query | safe }} 

✅ Safer
{{ query }} 

The template should become:
{% if query %} <h2> Search results for: {{ query }} </h2> {% endif %} 
Jinja2 can then apply its normal HTML escaping behavior.

10. Verify the Fix
After removing | safe, restart the application.
Then test the same harmless proof of concept:
<script>alert(1)</script> 
The browser should display the input as text instead of executing it.
The important observation is:
Before Fix: User Input ↓ Trusted HTML ↓ JavaScript Execution After Fix: User Input ↓ HTML Escaping ↓ Text ↓ No JavaScript Execution 

11. Security Lesson
The important lesson from this Lab is that XSS analysis is not simply about finding a payload.
A security tester should understand the complete data flow:
Source ↓ User-Controlled Input ↓ Processing ↓ Context ↓ Sink ↓ Browser Interpretation 
When investigating an XSS vulnerability, ask:

Where does the input originate?
Where does it go?
Is it transformed?
What context does it enter?
How does the browser interpret that context?

12. Key Takeaways
Reflected XSS occurs when user-controlled input is reflected into a response and interpreted as executable content.
The q parameter is the source of the attacker-controlled input.
Flask passes the value to the Jinja2 template.
The vulnerable rendering occurs in the HTML body context.
safe causes the value to be treated as trusted HTML.
Removing safe allows normal template escaping to protect the output.
XSS prevention must consider the context in which data is rendered.
CSP can provide additional defense-in-depth but should not replace fixing the underlying XSS vulnerability.

🛡️ Mitigation
Recommended defenses include:
1. Use automatic escaping
Allow the template engine to escape untrusted values:
{{ query }} 

2. Avoid unnecessary raw HTML rendering
Do not mark untrusted user input as safe HTML unless it has been properly sanitized and the application genuinely requires HTML input.

3. Use context-appropriate output encoding
The correct encoding strategy depends on where the data is inserted:
HTML Context Attribute Context JavaScript Context CSS Context URL Context 
Each context has different security requirements.

4. Use sanitization when HTML is actually required
If an application intentionally allows users to submit HTML, use a well-maintained HTML sanitization approach rather than simply disabling escaping.

5. Consider Content Security Policy
A strong Content Security Policy can provide an additional layer of protection, but it should be treated as defense-in-depth.

🏁 Lab Completed
You have successfully learned:
Source → Data Flow → Context → Unsafe Rendering → Reflected XSS → Mitigation
Next challenge:
Lab 02 — Reflected XSS in HTML Attribute Context