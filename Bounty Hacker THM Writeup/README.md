# Bounty Hacker-TryHackMe Writeup
This write-up covers my step-by-step approach to solving the Bounty Hacker room on TryHackMe. Using tools like Nmap, Gobuster, Hydra, and GTFOBins techniques, I enumerate the machine, uncover credentials through FTP, brute-force SSH access, and escalate privileges using allowed sudo commands.
Tools Used:<br> <br>
•	Nmap<br>
•	Gobuster<br>
•	Hydra<br>
•	GTFOBins techniques<br><br>
Starting with an nmap scan:<br><br>
<img width="939" height="436" alt="image" src="https://github.com/user-attachments/assets/2d812ff8-4ffb-4278-86c6-a18fc2fbc2fe" /><br><br>
Since it has port 80 open and is running on apache server, it must be a website. So I pasted the IP address and it opened up this:<br><br>
<img width="939" height="429" alt="image" src="https://github.com/user-attachments/assets/f4092b46-6d09-424e-b234-7753b119bf50" /><br><br>
I want to check if there are any hidden website I can access. I am going to use gobuster:<br>
gobuster dir --url http://10.201.68.29/ --wordlist /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --output gobuster_80.txt<br><br>
Here’s what I found:<br>
/images just had the crew image and a parent directory which obviously led to the main page again.<br>
/javascript was forbidden. I’ll need root privileges<br>
Gobuster was taking too long and it did not find anything else. I got impatient and explored ftp port.<br><br>
I logged into ftp using the cmd:<br>
	ftp 10.201.68.29<br><br>
<img width="901" height="555" alt="image" src="https://github.com/user-attachments/assets/0729613a-badd-4761-9ac3-ccb5e3456e5d" /><br><br>
I googled how to open the task.txt file. Apparently, you need to download it using the get cmd:<br>
	get task.txt<br><br>
And it gets downloaded in the local default directory.<br>
This was the content in the task.txt file:<br><br>
<img width="675" height="218" alt="image" src="https://github.com/user-attachments/assets/6f2207c4-b01f-403e-ba31-56ab391a8e40" /><br><br>
So lin must be the username for ssh. I can try exploring that. Well, I need a password for that.<br>
Wait, the question given on tryhackme said “What service can you bruteforce with the text file found?” and its answer is ssh. Since the word bruteforce is mentioned, does it mean we can use hydra? Well, let’s try that.<br><br>
	hydra -l lin -P /usr/share/wordlists/rockyou.txt ssh://10.201.68.29<br>
(https://datascientest.com/en/everything-about-brute-force-attack)<br><br>
It’s taking thousands of hours with this. Let me try something else. <br><br>
Wait, there was something else too besides tasks.txt right? Yes it was locks.txt. Let me get and check that.<br>
Oh that’s a wordlist! I can try that now.<br><br>
<img width="941" height="509" alt="image" src="https://github.com/user-attachments/assets/67735f1e-ade1-463a-a7e1-958a902fe70f" /><br><br>
Yay! Got the password. I can use this to get into ssh now.<br><br>
<img width="939" height="638" alt="image" src="https://github.com/user-attachments/assets/2496b270-601c-4f01-a833-977441ff9470" /><br><br>
Checked all the folders. Nothing there. So need to escalate privileges now.<br>
	sudo -l <br>
To check what lin can access to. They can access /bin/tar when they are root. So seached up and found this:<br><br>
<img width="940" height="211" alt="image" src="https://github.com/user-attachments/assets/41d73d2b-5df1-4afd-a9f9-159c6443b6e9" /><br>
(https://gtfobins.github.io/gtfobins/tar/)<br><br>
<img width="939" height="421" alt="image" src="https://github.com/user-attachments/assets/ca228e22-0533-4fa4-b77c-e03de062236f" /><br><br>
Got the root!<br><br>
<img width="326" height="150" alt="image" src="https://github.com/user-attachments/assets/6b2b7743-9ce3-48ff-b6ea-21189394a1c7" />
