---
title: Universal CTF
date: 2021-09-15
tags: ['Development', 'Cybersecurity', 'Python']
---

<div style="text-align:center">
    <img src="/images/universal-ctf/cover.jpg" />
</div>

## Description:

 Universal CTF is a project I started to help improve CTF experiences for beginners and speed up gathering results for veterans. Thanks to Universal CTF, you will no longer have to type out long commands over and over again, now you have a beautiful terminal based program to handle it all for you! 

You can view the project this blog post is based on here: https://github.com/azazelm3dj3d/universal-ctf

Welcome to a short blog post about a new program I wrote in Python to help those getting into CTFs in a more beginner-friendly way. This program offers a simple input system that allows you to run multiple commands without ever having to type out a long, tedious, command over and over again (yes, Nmap, I'm talking about you). I still recommend learning these tools and taking the time to study each of their flags and inner workings, but with this tool, hopefully, those just getting into Cybersecurity can have an easier transition.

At the moment, this tool offers a few simple commands: Nmap, Nikto, Gobuster, Sublist3r, Metasploit (msfconsole), and SQLMap. I have plans to add more commands in mulitple realms of security, but for now, this is a great start considering most CTFs always use at least Nmap and Gobuster to gain recon or enumeration information. Eventually, I would like to add some open-source tools built by the community like AutoRecon by [Tib3rius](https://github.com/Tib3rius/AutoRecon) or one of the hundreds of other incredible developers out there.

## Basic Understanding

Okay, so here's a quick example of what the code does:

<img src="/images/universal-ctf/img_1.jpg" />

Let's review the files that currently make up Universal CTF!

`config/` - This directory currently just holds the version information (at the time of writing this blog post, we are on v1.0.4)

`install.sh` - This file is a bash script that's set up to install every command available in the main Python script

`README.md` - This is used for GitHub to show some basic information about the project (you can view it here: https://github.com/azazelm3dj3d/universal-ctf)

`requirements.txt` - This file holds all of the required Python modules for this tool to work. Thankfully, I built this so that it currently only requires one module to be downloaded

`tools/` - this directory holds all of the tools that need to be downloaded. At the moment, only a few are stored since most tools like Nmap and Metasploit come on most PenTesting Linux systems nowadays (yes, Kali)

`universal-ctf/` - This directory holds our main Python script. We will get more into the code later, but for now, just know this is where it's stored

`update.sh` - A simple script that updates Universal CTF. It just pulls the newest version from GitHub

Now we know the file structure! Let's get into the actual program.

<img src="/images/universal-ctf/img_2.jpg" />

As you can see, there are currently 2 options: Recon and Exploit. This is how the list of commands are broken up, they are currently saved in dictionaries and printed out when selected

A quick list of the Recon commands are Nmap, Gobuster, Sublist3r, and Nikto. These commands are used fairly commonly, so they seemed like a decent place to start. Oh, and a cool mini thing which many people may not notice, but I think is pretty neat. The text "Enumeration Sensation" will always be printed as a randomly message everytime based on some phrases saved in a dictionary. I think it adds a nice little touch!

<img src="/images/universal-ctf/img_3.jpg" />

Let's try our first command! For this example, I will only be showing off Nmap, but all of these commands have been tested and should work with minimal effort on the user's end.

As you can see below, my super secret IP address was scanned with 0 open ports, and you'll notice that based on the questions we did scan with all default scripts and version detection. The Python script also lets us use our own flags, as you can see based on "Do you want to continue (y/n):" question. If you choose "n", than it would've prompted you for specific flags (and even wordlists for Gobuster!). This will become more apparent when testing the exploit options.

<img src="/images/universal-ctf/img_4.jpg" />

Now it's time to get into some exploitation. This feature is still very minimal and simple, so it only offers the following commands: Metasploit (msfconsole) and SQLMap. Both great commands, but obviously lacking in some places. Okay, if you remember before, I mentioned looking at selecting custom flags. Let's take a quick look at that for msfconsole and you'll see how the exploit commands work.

<img src="/images/universal-ctf/img_5.jpg" />

Here you'll notice that we booted up msfconsole with the -q flag, which just means it won't print the banner, version information, etc., so we can confirm it works. In the next image, you will see the output of printing msfconsole with -q flag.

<img src="/images/universal-ctf/img_6.jpg" />

And there you have it! Msfconsole successfully booted up in quiet mode.

<img src="/images/universal-ctf/img_7.jpg" />

Final author note: I am very proud of this project. It may not be perfect or the most glamourous, but I am extremely proud of it. I think it's a super cool project and will continue building on it for awhile. Any and all contributions are 100% welcome to help improve it! Thank you so much for taking time out of your day to read my blog post and I do hope you will try my tool and give any feedback you feel comfortable sharing. Thank you again, and I hope you have a wonderful rest of your day.