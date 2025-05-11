A custom webapp, introducing username enumeration, custom wordlists and a basic privilege escalation exploit.

![machine 1](https://github.com/user-attachments/assets/a9e7f6f2-faa1-4235-ba77-b4335a399da5)

## **Task 1 Reconnaissance**
using the nmap tool in my kali linux to find the primary information about the machine . Here the command below
![machine 2](https://github.com/user-attachments/assets/d8d69110-ebf7-4cf6-a2c8-e8cfda0fe9c7)


*QN 1: Which ports are open? (in numerical order)* 

  - 22 , 80 , 8080 .
    
*QN 2 : What programming language is the backend written in?*[[]]

- go
![machine 4](https://github.com/user-attachments/assets/6ceeccc5-4426-40bd-8f34-d89f54f393a4)

## Task 2: Investigate

Now  after that we know what's running, we need to investigate. With webapps, the normal process is to click around. Create an account, use the web app as a user would and play close attention to details.
![machine 5](https://github.com/user-attachments/assets/b2d7c722-7531-4fc9-b899-f3427693094e)



1. Create your own user account
![machine 6](https://github.com/user-attachments/assets/2a4a2d34-bec5-49f2-9d59-ac114d9bb4f6)

![machine 7](https://github.com/user-attachments/assets/d2427af4-c635-4946-add9-494d6938b13f)


	
   2. Log in to your account
![machine 8](https://github.com/user-attachments/assets/f9418b70-9d82-4a35-9419-42d657aefae5)

		
3.  Try and log in to an invalid user account
![machine 9](https://github.com/user-attachments/assets/3fc72089-97db-4f53-b6ef-f837bc2f31fc)

 
4. Try and log in to your account, with an incorrect password
    ![machine 10](https://github.com/user-attachments/assets/abd1868b-c962-4e25-be1d-dcb85f185af6)

     
 Notice the timing difference. This allows user enumeration. 
 
 *A **timing attack*** is a technique where an attacker measures **how long a system takes to respond** to different inputs — and uses this information to **guess secrets like usernames or passwords**.

## ** Task 3: Service Enumeration:**
Using **dirsearch** tool in kali linux to discovered Directories/Files: in the domain http://10.10.124.211/ . 

![machine 11](https://github.com/user-attachments/assets/4ae5662e-fe76-43e7-8698-b1ca5eaf9e04)


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
![machine 12](https://github.com/user-attachments/assets/c8296067-a96d-4b93-b510-b9dd2ae402e4)



Then send the request to **intruder** tool to find the valid username . Note That the username Wordlists have been provided in the lab .[ Download]( https://github.com/NinjaJc01/hackerNoteExploits)  . 

![machine 13](https://github.com/user-attachments/assets/84203349-8dc1-46a1-a9d1-40e0c0eda2b8)


Discovered user: `james`

 *QN: How many usernames from the list are valid?*
- 1
*QN: What are/is the valid username(s)?*
 - james
     
![machine 14](https://github.com/user-attachments/assets/6d9343bf-6417-4318-b193-1d07aefebf08)


We got the password **Hit** that will be our light to move forward with our attack. Using this hint we can craft or create a custom From this password hint, we can create a wordlist and (more) efficiently bruteforce the user's password.    

## Task 4: Attack Passwords

The HTTP POST request that we captured earlier tells us enough about the API that we can use **Hydra** to attack it.  
The API is actually designed to either accept Form data, or JSON data. The frontend sends JSON data as a POST request, so we will use this. Hydra allows attacking HTTP POST requests, with the HTTP-POST module.

- Using the hashcat-utilis to create a custom wordlist from the payload given in the lab.
![machine 15](https://github.com/user-attachments/assets/7975a40f-b026-4d68-b08b-1491ad13a261)

![machine 16](https://github.com/user-attachments/assets/b56cf194-5d47-4d46-99d3-d0f9c8b6e735)

![machine 17](https://github.com/user-attachments/assets/698baf59-4c2e-4aff-ba9d-e4479b213f30)

Using hydra to bruteforce tha web application password is ***blue7***
![machine 18](https://github.com/user-attachments/assets/bd0c0420-c083-4224-b558-18a081d6824a)

*QN : How many passwords were in your wordlist?*  
- **180**
![machine 19](https://github.com/user-attachments/assets/80c44f8f-2bd8-4899-85e0-bbba984c5edd)


*QN : What was the user's password?*
- blue7
		
- Required to login to the account using james account 
![machine 20](https://github.com/user-attachments/assets/013483cf-99c7-4d3a-b9ea-f356f757f4ea)

		
*QN: What was the user's password?*

- dak4ddb37b

Log in as the user to SSH with the credentials you have.
![machine 21](https://github.com/user-attachments/assets/7523b6b9-67ab-48c7-a8c9-058df0f0855c)


After login
![machine 22](https://github.com/user-attachments/assets/f8d763d7-e48f-4cf8-96c9-4c4652bee1b5)


*QN: What's the user flag?*
- thm{56911bd7ba1371a3221478aa5c094d68}

## Task 5: Escalate

Now that we have an SSH session, we can grab the user flag. But that shouldn't be enough for us, we need root.  
A good first step for privilege escalation is seeing if you can run sudo.

Using the command *
			**sudo -l***
![machine 23](https://github.com/user-attachments/assets/ee0b114e-79d9-461f-93e3-0b5747faaa5b)

 
Unfortunately, the current user cannot run any commands as root. You may have noticed, however, that when you enter your password you see asterisks.

![machine 24](https://github.com/user-attachments/assets/694eef7b-7ba4-423d-80e4-de60ec582116)

Using that setting ***pwdfeedback*** to search for a CVE in google.Found in exploit-db, 
![machine 25](https://github.com/user-attachments/assets/b6f04754-bd97-4931-af4a-642113359f97)


*QN: What is the CVE number for the exploit?*
- **CVE-2019-18634**

Let's find and download and exploit for that CVE, I found this one on [Github](https://github.com/saleemrashid/sudo-cve-2019-18634)
![machine 26](https://github.com/user-attachments/assets/2d2636ac-1b88-43d9-a39a-4dec4d20ef5b)


After download the exploit then we copy it to the vulnerable machine on ***/tmp*** directory since It's **writable by all users**, which means **any process**, including **unprivileged users**, can write to it.
![machine 27](https://github.com/user-attachments/assets/5e57923c-f72c-4cb0-82c7-b5c74af547c9)



Moving to the vulnerable machine to start our attack after delivery of exploits been done 
![machine 28](https://github.com/user-attachments/assets/02645555-5cba-43ba-b8c4-2c5fd3a33c61)

![machine 29](https://github.com/user-attachments/assets/1b74ebfa-2f83-4e53-acb6-b45eb697661e)

![machine 30](https://github.com/user-attachments/assets/c1589fea-6559-41da-9685-18b695f53436)

Execute using command ***./exploit***  

![machine 31](https://github.com/user-attachments/assets/ae98f950-fb3e-4395-be90-0f42b5907dff)

Boom...!!!  were in already with root privilege. Wer bosses now , hahahahahah.....!!!!
Now let's look for the flag. With *cat /root/root.txt*

![machine 32](https://github.com/user-attachments/assets/db61b8ff-e96c-4a60-87cc-d185a33f1480)


*QN:What is the root flag?*
- thm{af55ada6c2445446eb0606b5a2d3a4d

## Comment 

 How to Prevent It:

- **Always respond in the same amount of time**, no matter if login is successful or not.
    
- Use **constant-time comparisons** for passwords.
    
- Add **rate limiting** or **CAPTCHAs**.
    
