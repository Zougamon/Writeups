Ignite is a free room in TryHackMe which focuses on Web App/CMS exploitation! It was created by DarkStar7471. (https://tryhackme.com/room/ignite)

First, I will start with a standard Nmap scan to see what we can find on the target.\
nmap -sCV -oA /home/kali/Desktop/THM/Ignite {target_ip}

{Image}

I can see that the website is using Fuel CMS (Content Management System), so before even going to the website I did some research on potential vulnerabilities for Fuel CMS.\
After some searching, I found a Python script of ExploitDB which allowed for RCE. (https://www.exploit-db.com/exploits/50477)\
Now, I could immediatly go and attempt to use that exploit, but first I need to find where to execute it.\
Navigiating to the target website, I found the specific version of Fuel CMS being used, which is v1.4. This confirms that the exploit that I found will be able to work on the target.\
Next, I was able to look through the page and find a directory which also happened to be listed within the Nmap scan, http://x.x.x.x/fuel.\
The website listed this as an admin login, and gave default credentials which could potentially be used to log in.\

{Image}

I will go ahead and attempt to log in using the admin credentials: admin:admin\

{Image}

It worked!\
From here I could dig around the website and attempt to find something useful, however I also have the RCE exploit to take a look at.\

Lets go ahead and launch that up by going to the website listed above (https://www.exploit-db.com/exploits/50477), and downloading the script to our machine.\
Once I get the script downloaded, I want to run it against the target.\
I can do this by using python3 to execute the script.\

{Image}

Now I have a shell on the target, I can go ahead and attempt to get a more stable environment.\
My go to for reverse shells against websites with PHP is PentestMonkey's PHP Reverse Shell on GitHub. (https://github.com/pentestmonkey/php-reverse-shell).\
I already have the reverse shell on my kali machine, so I just need to navigate to the directory the file is in and start up a simple http server with python.\

{Image}

from the RCE exploit I used earlier, I can use wget to grab the reverse shell from my host.\

{Image}

Now that the reverse shell is on the target machine, I can open up a netcat listener on my machine.\

{Image}

Once the listener is up, I can navigate to the reverse shell by going to http://{target_ip}/phpreverseshell.php.\
This opens a connection from the target which my machine is listening for, creating a connection between the two and giving me a shell on the target which can be stabalized and escalated.\
Now that the connection is established, let's stabalize the shell using a simple python script.\

python3 -c 'import pty;pty.spawn("/bin/bash")'

We can grab the user flag from here, however I generally attempt escalation before grabbing flags.\
If I recall, the website mentioned a database and instructions related to setting that up.\
Let's go ahead and take a look at that database and see if we can get anything out of it.\

{Image}

Oh look! root credentials. Now I can escalate and own the system!\

{Image}

Now that we have ownership of the system, I can go ahead and find all the flags.\
The user flag is in the home directory for www-data: /home/www-data.\

{Image}

The root flag is in roots home directory: /root.\

{Image}

Thanks for reading!
