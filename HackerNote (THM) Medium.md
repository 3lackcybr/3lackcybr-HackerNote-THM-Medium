A custom webapp, introducing username enumeration, custom wordlists and a basic privilege escalation exploit.

![machine](/img/machine 1.png)

## **Task 1 Reconnaissance**
using the nmap tool in my kali linux to find the primary information about the machine . Here the command below

![[machine 2.png]]

*QN 1: Which ports are open? (in numerical order)* 

  - 22 , 80 , 8080 .
    
*QN 2 : What programming language is the backend written in?*[[]]

- go
-![[machine 4.png]]

## Task 2: Investigate

Now  after that we know what's running, we need to investigate. With webapps, the normal process is to click around. Create an account, use the web app as a user would and play close attention to details.

![[machine 5.png]]

1. Create your own user account     ![[machine 6.png]]
	  ![[machine 7.png]]
   2. Log in to your account

		![[machine 8.png]]
3.  Try and log in to an invalid user account

     ![[machine 9.png]]
4. Try and log in to your account, with an incorrect password
     ![[machine 10.png]]
 Notice the timing difference. This allows user enumeration. 
 
 *A **timing attack*** is a technique where an attacker measures **how long a system takes to respond** to different inputs — and uses this information to **guess secrets like usernames or passwords**.

## ** Task 3: Service Enumeration:**
Using **dirsearch** tool in kali linux to discovered Directories/Files: in the domain http://10.10.124.211/ . 

![[machine 11.png]]
**Findings:**

Discovered Directories/Files:

- `/index.html`
- `/not
-
## ## Vulnerability Analysis

After i created a test account and analyzed the responses for invalid usernames and passwords. Observed a timing delay when a valid username was used, enabling user enumeration.   An attacker can:

1. **Try many usernames.**
    
2. **Measure the response time.**
    
3. If the system takes **longer** to respond, it likely means the **username is valid** (because it took time to check the password).
    
4. Repeat the process with **passwords** to find the correct one.
---
**Tool Used:**  Burpsuite

![[machine 12.png]]

Then send the request to **intruder** tool to find the valid username . Note That the username Wordlists have been provided in the lab .[ Download]( https://github.com/NinjaJc01/hackerNoteExploits)  . 

![[machine 13.png]]
Discovered user: `james`

 *QN: How many usernames from the list are valid?*
- 1
*QN: What are/is the valid username(s)?*
 - james   
	
![[machine 14.png]]
We got the password **Hit** that will be our light to move forward with our attack. Using this hint we can craft or create a custom From this password hint, we can create a wordlist and (more) efficiently bruteforce the user's password.    

## Task 4: Attack Passwords

The HTTP POST request that we captured earlier tells us enough about the API that we can use **Hydra** to attack it.  
The API is actually designed to either accept Form data, or JSON data. The frontend sends JSON data as a POST request, so we will use this. Hydra allows attacking HTTP POST requests, with the HTTP-POST module.

- Using the hashcat-utilis to create a custom wordlist from the payload given in the lab.

![[machine 15.png]]

Using hydra to bruteforce tha web application password is ***blue7***

![[machine 18.png]]
*QN : How many passwords were in your wordlist?*  
- **180**
  ![[machine 19.png]]

*QN : What was the user's password?*
- blue7
		
- Required to login to the account using james account 
- ![[machine 20.png]]
		
*QN: What was the user's password?*

- dak4ddb37b

Log in as the user to SSH with the credentials you have.

![[machine 21.png]]

After login

![[machine 22.png]]
*QN: What's the user flag?*
- thm{56911bd7ba1371a3221478aa5c094d68}

## Task 5: Escalate

Now that we have an SSH session, we can grab the user flag. But that shouldn't be enough for us, we need root.  
A good first step for privilege escalation is seeing if you can run sudo.

Using the command *
			**sudo -l***
	![[machine 23.png]]
Unfortunately, the current user cannot run any commands as root. You may have noticed, however, that when you enter your password you see asterisks.

![[machine 24.png]]
Using that setting ***pwdfeedback*** to search for a CVE in google.Found in exploit-db, 
![[machine 25.png]]

*QN: What is the CVE number for the exploit?*
- **CVE-2019-18634**

Let's find and download and exploit for that CVE, I found this one on [Github](https://github.com/saleemrashid/sudo-cve-2019-18634)

![[machine 26.png]]

After download the exploit then we copy it to the vulnerable machine on ***/tmp*** directory since It's **writable by all users**, which means **any process**, including **unprivileged users**, can write to it.

![[machine 27.png]]

Moving to the vulnerable machine to start our attack after delivery of exploits been done 

![[machine 28.png]]

![[machine 29.png]]
Execute using command ***./exploit***  
![[machine 31.png]]
Boom...!!!  were in already with root privilege. Wer bosses now , hahahahahah.....!!!!
Now let's look for the flag. With *cat /root/root.txt*

![[machine 32.png]]

*QN:What is the root flag?*
- thm{af55ada6c2445446eb0606b5a2d3a4d

## Comment 

 How to Prevent It:

- **Always respond in the same amount of time**, no matter if login is successful or not.
    
- Use **constant-time comparisons** for passwords.
    
- Add **rate limiting** or **CAPTCHAs**.
    
