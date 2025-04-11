## What is a Timing Attack?
A timing attack is a technique where an attacker measures how long a system takes to respond to different inputs — and uses this information to guess secrets like usernames or passwords.

## How It Works in Credential Enumeration:

Imagine a login system checks your username first, then your password. If the username is wrong, it fails quickly. If the username is right but the password is wrong, it takes a little longer to respond.

An attacker can:

    - .Try many usernames.

    - Measure the response time.

    - If the system takes longer to respond, it likely means the username is valid (because it took time to check the password).

    - Repeat the process with passwords to find the correct one.

### Why It's Dangerous:

- Attackers can **slowly enumerate** valid usernames and passwords **without brute-force detection**.
    
- It works even if no useful error messages are shown — it only relies on **timing**.

### How to Prevent It:

- **Always respond in the same amount of time**, no matter if login is successful or not.
    
- Use **constant-time comparisons** for passwords.
    
- Add **rate limiting** or **CAPTCHAs**.
