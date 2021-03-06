This is a WriteUp on how to complete the room [Brooklyn Nine Nine](https://tryhackme.com/room/brooklynninenine) on [TryHackMe](https://tryhackme.com).

Note* I used Kali Linux to complete this room. The IP address of my room was 10.10.121.34, so that will be the IP you see in the writeup. Replace 10.10.121.34 with the IP of your target box.

This was a pretty enjoyable room to complete, not too difficult and pretty relaxing.\
The first step is to get some information about our target and do some enumeration.\
Let's start with...

Nmap
====

Nmap is a wonderful program to help identify and discover open ports and services on a given system. Let's run a scan and see what ports are open and what services/versions are available then save the output to a file for future reference.

**Command Used: nmap -sV -v -oN brooklyn_nine_nine_nmap 10.10.121.34**

How this works:\
nmap -- The command used to execute Nmap.\
-sV -- This means Nmap will run a service/version detection scan. These scans can take a while to run, but they return information about what services are running on each open port and what version they are.\
-v -- Verbose mode.\
-oN brooklyn_nine_nine_nmap -- This means we want to save the output to a file named brooklyn_nine_nine_nmap.

![nmap scan](https://jarrodrizor.com/wp-content/uploads/2021/02/nmap_brooklyn_nine_nine.png)

Let's check out the website on port 80 to see if we could find anything interesting. It seems to be just a scalable image with basic text.

Let's look at FTP port 21, which could be interesting.

FTP Anonymous Login
===================

It turns out that FTP supports Anonymous Login. Let's take advantage and see what is located on the server.

When completing rooms or doing CTFs; if you see FTP, try and see if you can get in using anonymous login. You might find an easy flag or information for what to do next.

![](https://jarrodrizor.com/wp-content/uploads/2021/02/anonymous_ftp_brooklyn_nine_nine.png)

If we run ls -l, we can see a file called **note_to_jake.txt**. If we run the FTP command **get** to pull down the file we can then **cat** the output.

![](https://jarrodrizor.com/wp-content/uploads/2021/02/cat_jake_note.png)

Learning Jake has a weak password let's see if we can brute force our way in with Hdyra.

Hydra
=====

Hydra is a very useful tool when we want to brute force passwords. Let's brute force SSH!

**Command Used: hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://10.10.121.34\
**\
How this works:\
hydra -- The command used to execute Hydra.\
-l -- This is the user we want to authenticate as.\
-P -- The wordlist we want to use when brute forcing.\
The last parameter is the target IP and using SSH protocol.

![](https://jarrodrizor.com/wp-content/uploads/2021/02/hydra_brooklyn_nine_nine.png)

This displayed that Hydra found a match with the password **987654321**.

Using our new-found user let's SSH in!

![ssh jake](https://jarrodrizor.com/wp-content/uploads/2021/02/ssh_jake_brooklyn_nine_nine.png)

Sudo
====

When we first SSH into a machine, we should run **ls -la** to see what files/hidden files are there and then run **sudo -l** to see if the user can use any commands using **superuser do**. Using **sudo -l** will show us that Jake has the ability to use the command **less**!

![jake sudo](https://jarrodrizor.com/wp-content/uploads/2021/02/sudo_jake_brooklyn_nine_nine.png)

flag 1 -- User flag
------------------

Running **ls -la** shows some interesting files in it, but we need to find the user.txt flag. Let's see if we can navigate to another home directory. Then try the /home/holt folder. Turns out this is where the user.txt flag is!

Simply **cat** our user.txt, and we have earned our first flag!

![user flag](https://jarrodrizor.com/wp-content/uploads/2021/02/user_flag_brooklyn_nine_nine.png)

flag 2 -- Root flag
------------------

Since we know that Jake has the ability to use **less** with **sudo**, let's see if [GTFOBins](https://gtfobins.github.io) has anything cool we can do to become root. Turns out it does! Here is a source on how we can use [less](https://gtfobins.github.io/gtfobins/less/#sudo) to become root!

If you haven't used GTFOBins I highly recommend bookmarking it and reading it, as it's a great source for learning how to take advantage of Linux/Unix binaries to perform privilege escalation.

Step 1 run **sudo less /etc/profile**

![](https://jarrodrizor.com/wp-content/uploads/2021/02/sudo_less_exploit_part_1_brooklyn_nine_nine.png)

Step 2 we need to input !/bin/sh and hit enter.

![](https://jarrodrizor.com/wp-content/uploads/2021/02/sudo_less_exploit_part_2_brooklyn_nine_nine.png)

Last, we need to **cat** out the root.txt located in /root/

![](https://jarrodrizor.com/wp-content/uploads/2021/02/sudo_less_exploit_part_3_brooklyn_nine_nine.png)

You can cheat and simply use **less** /root/root.txt, but that's no fun. I encourage you to always find a way to perform privilege escalation.

That completes the room! Well done! If you found this helpful, please send me a [tweet](https://twitter.com/JarrodR87) and tell me what you thought! Feedback is always appreciated!

Jarrod
