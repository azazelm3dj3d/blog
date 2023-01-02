---
title: How to Install Google Chrome on Kali Linux using a Bash Script
date: 2022-02-20
tags: ['Linux', 'Bash', 'Development']
---

<div style="text-align:center">
    <img src="/images/how-to-install-google-chrome-on-kali-linux-using-a-bash-script/cover.jpg" />
</div>

## Description:

 Today I will be showing you how to install Google Chrome on Kali Linux by writing a bash script from scratch. 

GitHub Link (Remember to follow me if you liked this code!): https://github.com/azazelm3dj3d/chrome-install

## Setting up environment

First, create a file called chrome-install.sh, and then open the file in your preferred IDE or editor.

After this file is created, we can begin writing the code.

The first few lines we will want to add:

```bash
echo "Downloading Google Chrome..."
```

This command will check if you already have Google Chrome installed on your system

```bash
if command -v google-chrome &> /dev/null;
then
    echo "Google Chrome is already installed"
    exit
fi
```

Now that we've made sure Google Chrome isn't installed, we can actually install it ourselves.

```bash
mkdir ~/Desktop/chrome_install && cd ~/Desktop/chrome_install
```

You will use the wget command to get the Google Chrome installation link.

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

This snippet will check whether the user trying to use the script is root or not. If you are not root, then you will at least need sudo privileges to run the next command in the script.

```bash
if [ "$EUID" -ne 0];
then
    echo "Sudo needs to be enabled"
    exit
fi
```

Finally, lets grab the Google Chrome package that we downloaded earlier and finally install it!

```bash
sudo dpkg -i google-chrome-stable_current_amd64.deb

echo "Congrats, you now have Chrome!"

echo "Version downloaded: " && google-chrome --version
```

Now we are going to open Google Chrome to verify a successful installation.

```bash
google-chrome &
```

## Extra - Uninstall Google Chrome


This is a simple script. All you will need to uninstall Google Chrome are the following commands:

```bash
sudo apt remove google-chrome-stable

rm -r ~/.config/google-chrome
```

Thank you so much for getting to the end of my blog post on How to install Google Chrome using a Bash Script. Here is the full source code w/ comments (also available on my GitHub) if you're a tl;dr type.

```bash
#!/bin/bash

# Creator: azazelm3dj3d
# Created: 08/07/2021
# Project: https://github.com/azazelm3dj3d/chrome-install

# Remember to make this file an executable by using: chmod +x chrome-install.sh

echo "Downloading Google Chrome..."

# Checks to see if you already have it installed

if command -v google-chrome &> /dev/null;
then
    echo "Google Chrome is already installed"
    exit
fi

# Creates a directory to store the .deb file

mkdir ~/Desktop/chrome_install && cd ~/Desktop/chrome_install

# Grabs the Chrome install from Google servers

wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

echo "Checking if you can use sudo..."

# Will ask for your password to make sure you have sudo permissions

if [ "$EUID" -ne 0];
then
    echo "Sudo needs to be enabled"
    exit
fi

# Installs Google package

sudo dpkg -i google-chrome-stable_current_amd64.deb

echo "Congrats, you now have Chrome!"

echo "Version downloaded: " && google-chrome --version

# Opens the browser for you. I know I'm so cool for doing this for you.

google-chrome &

# Uncomment these lines if you want to uninstall Google Chrome from your system

# Remember to comment all of the other lines before doing this

# sudo apt remove google-chrome-stable

# rm -r ~/.config/google-chrome
```