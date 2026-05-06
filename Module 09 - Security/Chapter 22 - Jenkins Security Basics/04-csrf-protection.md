# CSRF Protection: The Hidden Ticket

## 1. Introduction: The Identity Thief and the Forged Signature

Imagine you are a **Wealthy Investor**.
You have a personal assistant who handles your banking.
Every time you want to send money, you write a letter to the bank,
sign it with your specialized **Seal**, and your assistant delivers it.
The bank knows your seal, so they trust the letter and send the money.

One day, a **Cunning Identity Thief** learns about your routine.
The thief doesn't want to steal your seal—that would be too difficult.
Instead, they want to trick you into signing a letter you haven't read.
They hide a "Send $1,000,000 to the Thief" letter inside a stack of
normal business documents.
If you aren't careful, you might "Sign" that hidden letter without
ever seeing it.
Your signature is real, the bank trusts it, and the money is gone.
This is a **Cross-Site Request Forgery (CSRF)** attack.

In the world of the Web, your "Signature" is your **Login Cookie**.
If you are logged into Jenkins in one browser tab, and you visit a
malicious website in another tab, that website can try to "Trick"
your browser into sending a command to Jenkins (like "Delete all jobs").
Because you are logged in, Jenkins sees your cookie and thinks the
command is from **You**.

## 2. The Crumb: Jenkins' Defense

To solve this problem, Jenkins uses a "Secret Ticket" called a
**Crumb**.
A crumb is a long, random string that Jenkins generates specifically
for your current session.

Every time you click a button in the Jenkins UI, the browser must
send the crumb along with your command.
If a malicious website tries to send a command, it won't have the
crumb.
Jenkins will look at the command, see that the "Ticket" is missing,
and immediately **Reject** the request.

---

## 3. The "Crumb Issuer"

In the Global Security settings, a senior engineer ensures that the
**Crumb Issuer** is enabled.

### Why is this important?
-   **Protection:** It stops hackers from using your login session
    to perform actions on your behalf.
-   **API Access:** If you are writing a script or an AWS Lambda
    function that needs to talk to Jenkins, you must first "Ask"
    Jenkins for a crumb, and then include that crumb in every
    following request.

Mastering the "Crumb" is a sign of a senior-level understanding
of web security and API architecture.

---

## Summary

- CSRF (Cross-Site Request Forgery) is an attack that tricks a logged-in user into performing unwanted actions
- In a CSRF attack, a malicious website "borrows" the user's session cookie to send forged commands
- Jenkins defends against these attacks using a "secret ticket" known as a "Crumb"
- Every legitimate request to the Jenkins server must include a valid crumb for that specific session
- The "Crumb Issuer" is a mandatory security setting for protecting production Jenkins controllers
- Scripts and external APIs must be programmed to retrieve and include a crumb in their requests
- CSRF protection is a critical layer for ensuring the integrity of the Jenkins command chain

In the next chapter, **Chapter 23: Credentials Management**, you will learn how to protect the most sensitive data in your system.
