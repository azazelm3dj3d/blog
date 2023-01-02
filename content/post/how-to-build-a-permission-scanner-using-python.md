---
title: How to Build a Permission Scanner using Python
date: 2022-04-12
tags: ['Python', 'Development']
---

<div style="text-align:center">
    <img src="/images/how-to-build-a-permission-scanner-using-python/cover.jpg" />
</div>

# Description:

This permissions scanner is a project I created back in April 2022 and has helped tremendously in CTFs and other situations that require me to locate all files and directories accessible by the currently logged in user. This script will be useable on both Windows and Linux. This project uses an octal pattern to search the Linux filesystem and a username pattern for Windows.

*This is not a Python educational blog post. We will move fairly quickly and not spend too much time with basic Python concepts.

## Introduction

This permissions scanner is a simple tool created as a way to quickly locate any files or directories you may be able to exploit (as the logged-in user) or if you wanna just run a security check to see what files can be accessed on your system, this should help!

If you wanna just study the code, the repository is available here: https://github.com/azazelm3dj3d/us3r-unl0ck3d

## Step 1

Let's start off with the imports. This script will not require any external libraries or dependencies. If you're running an up-to-date version of Python3, it should work without any issues.

```python
# Allows us to use system commands directly in our script
from subprocess import getoutput

# Argparse - Allows use to add "flags" or "options" to our script when running it (more on this later)
# Platform - Allows us to define the platform our script will run on (ex: Windows, Linux)
import argparse, platform
```

Now that we've imported our libraries, let's setup our options using argparse.
```python
# Initializes our argparse instance
parser = argparse.ArgumentParser()

# Our many options for the script, you'll see why these are important later. The descriptions should give a good idea of what they do
parser.add_argument('-o', '--octal', type=int, default=777, required=False, help="Type your octal as an integer (Ex: 777)")
parser.add_argument('-f', '--file', default=False, required=False, help="Store the output in a file (Format: log_<OCTAL>.txt)", action="store_true")
parser.add_argument('-v', '--verbose', default=False, required=False, help="If outputting to a file, this flag will need to be triggered to also print to the console", action="store_true")
parser.add_argument('-c', '--color', default=False, required=False, help="Add some color! (True by default) | Linux Only", action="store_true")
parser.add_argument('-r', '--recursive', default=False, required=False, help="Recursively search the entire specified directory (May take a long time) | Windows Only", action="store_true")
args = parser.parse_args()
```

A few terms you not be familiar with:

octal - A 3-digit number representing the Linux system permissions (check here for more info: https://chmod-calculator.com/). Each number represents read, write, or execute permissions. Ex: 777 gives full permissions across all users.

## Step 2

Okay, this next part is simple. Time to create a template.

```python
class Us3rUnl0ck3d:
    def main(self):

        # Handles the conversion from string to integer - code will be added in the next section
        def convert_permissions(oct):

            # Code goes here
        
        # This is just a simple method created for color output on Linux
        def color_scheme(r, g, b, text):
            if args.color and platform.system() != 'Windows':
                return "\033[38;2;{};{};{}m{} \033[38;2;255;255;255m".format(r, g, b, text)

if __name__ == '__main__':
    U3 = Us3rUnl0ck3d()
    U3.main()
```

Notice we put the `!=` check against Windows in the `def color_scheme(r, g, b, text):` method. This is because PowerShell doesn't have the same color configuration as most Linux terminals. You'll also notice the `args.color` check, this is from the argument parser above. We can run this at the end by using the `-c` flag.

## Step 3

Next step is also pretty simple. We're gonna setup our options and a few simple system commands so we know who the current user is.

I know this looks like a lot, but let's break it down in the comments.

```python
class Us3rUnl0ck3d:
    def main(self):
        def convert_permissions(oct):

            int_to_str_conversion = ""
            perm_values = [(4,"r"),(2,"w"),(1,"x")]

            # This nested for loop iterates over each number to output the equaivalent letter associated with the permissions octal
            for int_octal in [int(o) for o in str(oct)]:
                for x, str_octal in perm_values:
                    if int_octal >= x:
                        int_to_str_conversion += str_octal
                        int_octal -= x
                    else:
                        int_to_str_conversion += '-'
                    
            return int_to_str_conversion

        def color_scheme(r, g, b, text):
            if args.color and platform.system() != 'Windows':
                return "\033[38;2;{};{};{}m{} \033[38;2;255;255;255m".format(r, g, b, text)
        
        # These 2 snippets will get you the currently logged in user by using the "whoami" command and the "Get-WMIObject" command, grabbing the username
        whoami = getoutput("whoami")
        whoami_win = getoutput("powershell -Command  (Get-WMIObject -ClassName Win32_ComputerSystem).Username")

        # This creates a variable that saves our function from before, using the -o flag argument
        permissions = convert_permissions(args.octal)

        # Sets our color and checks which system we're using + sets all of our options for when running the script. This allows us to search either the entire filesystem, the home directory, or we can specify a directory to search
        if args.color and platform.system() != 'Windows':
            print("[ {0}]  {1}   Entire Root System (Parameters: /*)"
                .format(color_scheme(0, 255, 0, "0"), color_scheme(0, 255, 0, "=>")))
            
            print("[ {0}]  {1}   Home Directory (Parameters: ~)"
                .format(color_scheme(0, 255, 0, "1"), color_scheme(0, 255, 0, "=>")))
            
            print("[ {0}]  {1}   Custom Directory (Ex: /etc)"
                .format(color_scheme(0, 255, 0, "2"), color_scheme(0, 255, 0, "=>")))
        elif platform.system() == 'Windows':
            print("[{0}]  {1}   Entire Root System (Parameters: C:\*)"
                .format("0", "=>"))
            
            print("[{0}]  {1}   Home Directory (Parameters: ~)"
                .format("1", "=>"))
            
            print("[{0}]  {1}   Custom Directory (Ex: \Program Files)"
                .format("2", "=>"))
        else:
            print("[{0}]  {1}   Entire Root System (Parameters: /*)"
                .format("0", "=>"))
            
            print("[{0}]  {1}   Home Directory (Parameters: ~)"
                .format("1", "=>"))
            
            print("[{0}]  {1}   Custom Directory (Ex: /etc)"
                .format("2", "=>"))
        
        # Waits for the user to select an option
        option = input("Choose Option: ")

if __name__ == '__main__':
    U3 = Us3rUnl0ck3d()
    U3.main()
```

## Step 4

Now it's time to break this script up, option by option. We'll start with option 0, which will allow the user to search the entire root filesystem.

Our first option checks which system the user is currently on (like all of our options), then runs a system command based on that. This option runs a `Get-ChildItem -Recurse` command in PowerShell, this'll search the entire root filesystem and check all internal directories for the logged-in user. This is then piped to mulitple other commands that'll search the filesystem using common string locator commands.

For the Linux commands (`else`), we're just using `ls -la`, which lists the directories and files with the octal patterns visible. Then we'll `grep` the user and permissions saved earlier.

You'll notice us calling multiple custom arguments using the following syntax: `args.*`. This way can call our values or booleans, provided by the user.

```python
if option == '0':
    if platform.system() == 'Windows':
        if args.recursive:
            print("This might take awhile...")
            access_granted = getoutput(f"powershell -Command Get-ChildItem -Recurse C:\* | powershell -Command Get-Acl C:\* | powershell -Command findstr '{whoami_win}'")
        else:
            access_granted = getoutput(f"powershell -Command Get-ChildItem C:\* | powershell -Command Get-Acl C:\* | powershell -Command findstr '{whoami_win}'")
        
        print(f"\nSearching system for username: {whoami_win}")
    else:
        access_granted = getoutput(f"ls -la /* | grep '{whoami}' | grep '{permissions}'")
        
        print(f"\nSearching system for username: {whoami}")
        print(f"Permissions Set: {permissions}")
```

Similar structure as above, but instead searches the home directory, this is apparent by the tilda (~) in the command.

Option 1:
```python
elif option == '1':
    if platform.system() == 'Windows':
        if args.recursive:
            print("This might take awhile...")
            access_granted = getoutput(f"powershell -Command Get-ChildItem -Recurse ~ | powershell -Command Get-Acl ~ | powershell -Command findstr '{whoami_win}'")
        else:
            access_granted = getoutput(f"powershell -Command Get-ChildItem ~ | powershell -Command Get-Acl ~ | powershell -Command findstr '{whoami_win}'")
        
        print(f"\nSearching system for username: {whoami_win}")
    else:
        access_granted = getoutput(f"ls -la ~ | grep '{whoami}' | grep '{permissions}'")
    
        print(f"\nSearching system for username: {whoami}")
        print(f"Permissions Set: {permissions}")
```

Everything is the same as above, the only difference is that we're not searching a directory specified by the user. Awesome, right!?

Option 2:
```python
elif option == '2':
    dir_name = input("Directory to search: ")

    if platform.system() == 'Windows':
        if args.recursive:
            print("This might take awhile...")
            access_granted = getoutput(f"powershell -Command Get-ChildItem -Recurse '{dir_name}' | powershell -Command Get-Acl '{dir_name}' | powershell -Command findstr '{whoami_win}'")
        else:
            access_granted = getoutput(f"powershell -Command Get-ChildItem '{dir_name}' | powershell -Command Get-Acl '{dir_name}' | powershell -Command findstr '{whoami_win}'")
        
        print(f"\nSearching system for username: {whoami_win}")
    else:
        access_granted = getoutput(f"ls -la '{dir_name}' | grep '{whoami}' | grep '{permissions}'")
    
        print(f"\nSearching system for username: {whoami}")
        print(f"Permissions Set: {permissions}")

# And we end it all with an else check if the user does not select an available option
else:
    if args.color and platform.system() != 'Windows':
        print("\n{0}\n".format(color_scheme(255, 0, 0, "Unable to search or access system")))
    else:
        print("\n{0}\n".format("Unable to search or access system"))
```

Now that the permissions scanner is technically complete, let's make a big push to add some cool additions.

Check the comments below as we break down this snippet of code.

```python
try:
    if access_granted != None:

        # Prints out the info about our search

        if args.color and platform.system() != 'Windows':
            print("\n=== {0} ===\n".format(color_scheme(0, 204, 0, "SEARCH INFO")))
        else:
            print("\n=== {0} ===\n".format("SEARCH INFO"))
        
        if platform.system() == 'Windows':
            print(f"The following files are accessible based the set parameters: {whoami_win}\n")
        else:
            print(f"The following files are accessible based the set parameters: {permissions}\n")
        
        # Checks if we set the verbose flag (-v), which allows every directory or file we find to be printed out to the screen. We also pass the -f to output to a file.
        if args.file and args.verbose:
            if platform.system() == 'Windows':
                print("Path     Owner       Access")
                print("-" * 50)
                print(access_granted)
            else:
                print(access_granted)
        
        # Only saved to a file, not output is printed to the screen
        elif args.file and not args.verbose:
            print("\nAll data is saved in the generated file")
        else:
            if platform.system() == 'Windows':
                print("Path     Owner               Access")
                print("-" * 50)
                print(access_granted)
            else:
                print(access_granted)

        if args.file:
            if platform.system() == 'Windows':
                with open(f'log_{whoami_win}.txt', 'w') as f:
                    f.write("Path     Owner       Access")
                    f.write("-" * 50)
                    f.write(access_granted)
            else:
                with open(f'log_{args.octal}.txt', 'w') as f:
                    f.write(access_granted)
    else:
        if args.color and platform.system() != 'Windows':
            print("\n{0}\n".format(color_scheme(255, 0, 0, "No data was generated")))
        else:
            print("No data was generated")
except UnboundLocalError as e:
    if args.color and platform.system() != 'Windows':
        print("\n{0} {1}\n".format(color_scheme(255, 0, 0, "[ERROR] =>"), e))
    else:
        print("[ERROR] => {0}".format(e))
```

This script may be a little more complicated if you're not familiar with the Linux or Windows filesystem, but if you have some Python experience, it should be fairly straightforward in what it does by running system commands against the system and locating relevant information for us to use.

## Entire Script

Thanks for sticking with me through this project! Below you'll find the full script (without the comments). You can also visit my GitHub, which houses the full project.

Repo: https://github.com/azazelm3dj3d/us3r-unl0ck3d

```python
from subprocess import getoutput
import argparse, platform

parser = argparse.ArgumentParser()
parser.add_argument('-o', '--octal', type=int, default=777, required=False, help="Type your octal as an integer (Ex: 777)")
parser.add_argument('-f', '--file', default=False, required=False, help="Store the output in a file (Format: log_<OCTAL>.txt)", action="store_true")
parser.add_argument('-v', '--verbose', default=False, required=False, help="If outputting to a file, this flag will need to be triggered to also print to the console", action="store_true")
parser.add_argument('-c', '--color', default=False, required=False, help="Add some color! (True by default) | Linux Only", action="store_true")
parser.add_argument('-r', '--recursive', default=False, required=False, help="Recursively search the entire specified directory (May take a long time) | Windows Only", action="store_true")
args = parser.parse_args()

version = "0.2.11"

class Us3rUnl0ck3d:

    def main(self):

        def convert_permissions(oct):
            int_to_str_conversion = ""
            perm_values = [(4,"r"),(2,"w"),(1,"x")]
            
            for int_octal in [int(o) for o in str(oct)]:
                for x, str_octal in perm_values:
                    if int_octal >= x:
                        int_to_str_conversion += str_octal
                        int_octal -= x
                    else:
                        int_to_str_conversion += '-'
                    
            return int_to_str_conversion
        
        def color_scheme(r, g, b, text):
            if args.color and platform.system() != 'Windows':
                return "\033[38;2;{};{};{}m{} \033[38;2;255;255;255m".format(r, g, b, text)

        whoami = getoutput("whoami")
        whoami_win = getoutput("powershell -Command  (Get-WMIObject -ClassName Win32_ComputerSystem).Username")
        permissions = convert_permissions(args.octal)

        if args.color and platform.system() != 'Windows':
            print("[ {0}]  {1}   Entire Root System (Parameters: /*)"
                .format(color_scheme(0, 255, 0, "0"), color_scheme(0, 255, 0, "=>")))
            
            print("[ {0}]  {1}   Home Directory (Parameters: ~)"
                .format(color_scheme(0, 255, 0, "1"), color_scheme(0, 255, 0, "=>")))
            
            print("[ {0}]  {1}   Custom Directory (Ex: /etc)"
                .format(color_scheme(0, 255, 0, "2"), color_scheme(0, 255, 0, "=>")))
        elif platform.system() == 'Windows':
            print("[{0}]  {1}   Entire Root System (Parameters: C:\*)"
                .format("0", "=>"))
            
            print("[{0}]  {1}   Home Directory (Parameters: ~)"
                .format("1", "=>"))
            
            print("[{0}]  {1}   Custom Directory (Ex: \Program Files)"
                .format("2", "=>"))
        else:
            print("[{0}]  {1}   Entire Root System (Parameters: /*)"
                .format("0", "=>"))
            
            print("[{0}]  {1}   Home Directory (Parameters: ~)"
                .format("1", "=>"))
            
            print("[{0}]  {1}   Custom Directory (Ex: /etc)"
                .format("2", "=>"))

        option = input("Choose Option: ")

        if option == '0':
            if platform.system() == 'Windows':
                if args.recursive:
                    print("This might take awhile...")
                    access_granted = getoutput(f"powershell -Command Get-ChildItem -Recurse C:\* | powershell -Command Get-Acl C:\* | powershell -Command findstr '{whoami_win}'")
                else:
                    access_granted = getoutput(f"powershell -Command Get-ChildItem C:\* | powershell -Command Get-Acl C:\* | powershell -Command findstr '{whoami_win}'")
                
                print(f"\nSearching system for username: {whoami_win}")
            else:
                access_granted = getoutput(f"ls -la /* | grep '{whoami}' | grep '{permissions}'")
                
                print(f"\nSearching system for username: {whoami}")
                print(f"Permissions Set: {permissions}")
        elif option == '1':
            if platform.system() == 'Windows':
                if args.recursive:
                    print("This might take awhile...")
                    access_granted = getoutput(f"powershell -Command Get-ChildItem -Recurse ~ | powershell -Command Get-Acl ~ | powershell -Command findstr '{whoami_win}'")
                else:
                    access_granted = getoutput(f"powershell -Command Get-ChildItem ~ | powershell -Command Get-Acl ~ | powershell -Command findstr '{whoami_win}'")
                
                print(f"\nSearching system for username: {whoami_win}")
            else:
                access_granted = getoutput(f"ls -la ~ | grep '{whoami}' | grep '{permissions}'")
           
                print(f"\nSearching system for username: {whoami}")
                print(f"Permissions Set: {permissions}")
        elif option == '2':
            dir_name = input("Directory to search: ")

            if platform.system() == 'Windows':
                if args.recursive:
                    print("This might take awhile...")
                    access_granted = getoutput(f"powershell -Command Get-ChildItem -Recurse '{dir_name}' | powershell -Command Get-Acl '{dir_name}' | powershell -Command findstr '{whoami_win}'")
                else:
                    access_granted = getoutput(f"powershell -Command Get-ChildItem '{dir_name}' | powershell -Command Get-Acl '{dir_name}' | powershell -Command findstr '{whoami_win}'")
                
                print(f"\nSearching system for username: {whoami_win}")
            else:
                access_granted = getoutput(f"ls -la '{dir_name}' | grep '{whoami}' | grep '{permissions}'")
            
                print(f"\nSearching system for username: {whoami}")
                print(f"Permissions Set: {permissions}")
        else:
            if args.color and platform.system() != 'Windows':
                print("\n{0}\n".format(color_scheme(255, 0, 0, "Unable to search or access system")))
            else:
                print("\n{0}\n".format("Unable to search or access system"))
        
        try:
            if access_granted != None:

                if args.color and platform.system() != 'Windows':
                    print("\n=== {0} ===\n".format(color_scheme(0, 204, 0, "SEARCH INFO")))
                else:
                    print("\n=== {0} ===\n".format("SEARCH INFO"))
                
                if platform.system() == 'Windows':
                    print(f"The following files are accessible based the set parameters: {whoami_win}\n")
                else:
                    print(f"The following files are accessible based the set parameters: {permissions}\n")

                if args.file and args.verbose:
                    if platform.system() == 'Windows':
                        print("Path     Owner       Access")
                        print("-" * 50)
                        print(access_granted)
                    else:
                        print(access_granted)
                elif args.file and not args.verbose:
                    print("\nAll data is saved in the generated file")
                else:
                    if platform.system() == 'Windows':
                        print("Path     Owner               Access")
                        print("-" * 50)
                        print(access_granted)
                    else:
                        print(access_granted)

                if args.file:
                    if platform.system() == 'Windows':
                        with open(f'log_{whoami_win}.txt', 'w') as f:
                            f.write("Path     Owner       Access")
                            f.write("-" * 50)
                            f.write(access_granted)
                    else:
                        with open(f'log_{args.octal}.txt', 'w') as f:
                            f.write(access_granted)
            else:
                if args.color and platform.system() != 'Windows':
                    print("\n{0}\n".format(color_scheme(255, 0, 0, "No data was generated")))
                else:
                    print("No data was generated")
        except UnboundLocalError as e:
            if args.color and platform.system() != 'Windows':
                print("\n{0} {1}\n".format(color_scheme(255, 0, 0, "[ERROR] =>"), e))
            else:
                print("[ERROR] => {0}".format(e))
        

if __name__ == '__main__':
    U3 = Us3rUnl0ck3d()
    U3.main()
```