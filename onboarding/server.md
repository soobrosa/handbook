---
title: How to use the haflinger server
teaching: 0
exercises: 0
questions:
  - How to connect to haflinger server?
  - What is the structure of the server?
  - What is the general workflow on the server?
objectives:
  - Learn how to set up connection with the server
  - Understand the basic specifications of our server
  - Study commands that are useful when working on the server
description: How to use the haflinger server
---

# Server

> ### Overview
>
> Questions
>
> * How to connect to haflinger server?
> * What is the structure of the server?
> * What is the general workflow on the server?
>
> Objectives
>
> * Learn how to set up connection with the server
> * Understand the basic specifications of our server
> * Study commands that are useful when working on the server

## The server and some basic information

Microdata currently uses one server.

* haflinger: haflinger.ceu.hu - function: STATA/Matlab/Python with graphical interface

The haflinger server has 504GB memory and 112 cores.

## Connecting to the server from different local operating systems

First, you have to be connected to the CEU network through VPN. You can use the AnyConnect client or the openconnect package depending on your OS. For more information on VPN usage please visit [https://ceuedu.sharepoint.com/sites/itservices/SitePages/vpn.aspx](https://ceuedu.sharepoint.com/sites/itservices/SitePages/vpn.aspx)

You can access the shell \(command line\) on haflinger.ceu.hu by using a Secure Shell \(ssh\) client, such as Putty \([http://docs.microdata.io/putty](http://docs.microdata.io/putty)\). On UNIX-like systems the built-in ssh package allows you to connect without any additional software. This is where you can change your password, or where you can start batch jobs from the shell.

You can connect to the graphical interface \(windows-like\) on haflinger.ceu.hu via a VNC client. On UNIX-like systems using X-server \(e.g. Ubuntu\) you can simply allow X11 forwarding in an ssh connection by using the `-X` optional argument and don't need a VNC client.

### Linux

You can connect to the server from a Terminal window using the following command \(substitute your username and port number appropriately\):

```text
  $ ssh USER@haflinger.ceu.hu -p PORT -X
```

### MacOS

You can connect to the server from a Terminal window using the following command \(substitute your username and port number appropriately\)

```bash
  $ ssh USER@haflinger.ceu.hu -p PORT
```

For a graphical server connection a useful tool is XQuartz. Using XQuartz you can enable X11 forwarding. In an XQuartz terminal you can connect to the graphical server by issuing the following command: \(substitute your username and port number appropriately\)

```bash
  $ ssh USER@haflinger.ceu.hu -p PORT -y
```

### Windows

PuTTy provides a CLI for the server.

#### VNC

On Windows you can download a simple REAL VNC viewer from: [vncviewer](https://cp.webgalaxy.hu/haflinger/vncviewer.exe) After installation you have to use the following server address along with your server password: `haflinger.ceu.hu:VNCPORT`

A good example how you can start the viewer from the cmd. You have to change the "%vnc\_port%" part to your own port number.

```bash
  $ vncviewer.exe -SecurityNotificationTimeout=0 -WarnUnencrypted=0 -Quality=High -Scaling=100%x100% haflinger.ceu.hu:%vnc_port%
```

#### Xserver and git-bash

You can also connect to the X server from git-bash terminal by the help of [vcxsrv](https://sourceforge.net/projects/vcxsrv/files/).  
  
Install the application then open`Xlaunch`and use the default settings: Multiple window, start no client, clipboard. Just click next as long as an X appears on the tray. X server has started. 

Open git-bash and type `cd $HOME` You are now in your Windows home folder. Just check it with `pwd`Create a new file called .bash\_profile: `nano .bash_profile` Into the profile file save the following options: `export DISPLAY=localhost:0.0`Save the file and restart git-bash.

Connect to your remote server using the following command:

```text
$ ssh USER@haflinger.ceu.hu -p PORT -Y -v
```

The option`-v` is used to diagnose the X connection problems. You can leave later if everything works well.

Log in with your own haflinger password then e.g. type `xstata-mp`. The Stata comes up in a new window. Be careful this is not a VNC session, if you close the Stata window you close the session. 

### Private and public keys for easier connection

For easier server access you can create private/public key pairs as follows:

1. Start the key generation program by typing `ssh-keygen` on your local computer
2. Enter the path to the file where it is going to be located. Make sure you locate it in your `.ssh` folder and name it as `microdata_kulcs` \(or any alternative filename\).
3. Enter a Passphrase or just simply press Enter. The public and private keys are created automatically. The public key ends with the string `.pub`.
4. Copy the public key to the `$HOME/USER/.ssh` folder on the server. \(substitute your username appropriately\)
5. An alternative solution for point 4 is using the following code: `ssh-copy-id -i ~/.ssh/microdata_kulcs USER@haflinger.ceu.hu -p PORT`. \(a useful [source](https://www.ssh.com/ssh/keygen/) for the whole process\)

Finally, you can alias the command that connects you to server:

#### MacOS

Copy the following text into the `config` file which is located in your .ssh folder: \(substitute your usernames and port number appropriately\)

```bash
Host haflinger
        HostName haflinger.ceu.hu
        User USER
        Port PORT
        IdentityFile /Users/LOCAL_USER/.ssh/microdata_kulcs
```

This allows you to connect to the haflinger server by typing the `ssh haflinger` command.

#### Linux

Add the following lines to your `.bashrc` file located in your home folder \(by typing `nano .bashrc`\):

```bash
alias haflinger='ssh USER@haflinger.ceu.hu -p PORT -X'
```

This allows you to connect to the haflinger server by typing the `haflinger` command.

#### Windows

Open git-bash and type`cd $HOME`. Create a new folder called .ssh:`mkdir .ssh` and cd into the .ssh folder. From the folder run `ssh-keygen.exe` Now you can follow the "For easier server access you can create private/public key pairs steps" second and third point. 

If you don't have the .ssh folder at`$HOME/USER` at the server you have to create it there also. Login from git-bash. The logout command is `exit.` Use it now! 

When you generated the public and private keys called `microdata_kulcs`push it to the server from your local machine .ssh folder. 

`user@DESKTOP: ssh-copy-id -i microdata_kulcs -p PORT USER@haflinger.ceu.hu` 

If you done it well a file called authorized\_keys created in the `/home/USER/.ssh/authorized_keys`

Connect back to the server and change the folder permissions: 

`user@haflinger: chmod 700 ~/.ssh`

`user@haflinger: chmod 600 ~/.ssh/authorized_keys`

Exit from the server.

Open the .bash\_profile with nano and put an alias into the profile file: 

`alias haflinger='ssh -p PORT -Y -v USER@haflinger.ceu.hu'`

Now your bash\_profile consists two line the the export display and the alias. 

The final step is to add the ssh agent to the profile file. The agent handles the private and public keys. 

The final .bash\_profile looks like this: 

```bash
export DISPLAY=localhost:0.0

alias haflinger='ssh -p PORT -Y -v USER@haflinger.ceu.hu'

if [ -z "$SSH_AUTH_SOCK" ] ; then 
  eval ssh-agent -s 
  ssh-add ~/.ssh/microdata_kulcs 
fi
```

Start the Xlaunch. Restart the git-bash and type `haflinger` If everything worked well you are now on the haflinger X server.

## Structure of the server and general workflow

When connecting to the server, you are directed to your home folder \`/home/USER\_NAME'. Only you have access to your home folder. This folder should only be used to store configuration files \(like ssh keys, etc.\).

For any project work you should be working in your sandbox located at `/srv/sandbox/USER_NAME`. Working in your sandbox allows others to check your work and develop projects collaboratively. Folders with limited access for certain projects are located in `/srv/project`. In case you need a separate project folder with access limitation you should contact a project manager.

When saving your bead, a copy of your work is created in .zip format in one of the beadboxes. The beadboxes are located at `/srv/bead-box/`. For more information on the use of bead, please visit the corresponding episode on this website.

### Creating alias to your sandbox

You can create aliases that simplifiy your access to you sandbox and other directories. For that, you need to add the following commands to your `.bashrc` file: \(substitute your username appropriately\)

```text
alias sandbox='cd /srv/sandbox/USER_NAME'
```

The `.bashrc` is located in your home folder \(`home/USER`\).

Then, you can access your sandbox by typing `sandbox` to the command line.

You can create similar aliases for other frequently used locations similarly.

## Useful server tools

### File transfer to the server

To move data between your computer and the server, you need an SFTP or SCP client. You have access to your home folder, and you may access shared data and project folders. You can choose from a wide variety of SFTP clients. A few of these are the following:

* Filezilla - [https://filezilla-project.org/](https://filezilla-project.org/)
* On Windows you can use WinScp - [https://winscp.net/eng/index.php](https://winscp.net/eng/index.php)

To access the files on the server, provide the following sftp address to your client along with your username, password, and appropriate port number:

`sftp://haflinger.ceu.hu`

It is worth noting that on Ubuntu systems you don't need any additional client for file transfer. Just open a file navigator, go to Other locations, and connect to the server by issuing it's sftp address - e.g. `sftp://USER@haflinger.ceu.hu:PORT`. You will automatically be prompted for your username and password, and you can navigate on the server just like on your own computer.

### Screen

Working in screen allows users to exit the server without terminating the running processes. Processes started in a screen will also continue running in case of a connection issue. Therefore, you should always work in screen when running complex programs that run for longer time. For instructions on how to open and close a screen window, see the 'Useful server commands' section below.

### Virtual environment

When your code requires specific python packages, you should download them to a virtual environment. The Python environment on the server incorporates only the most basic Python packages so it is always recommended to work in a virtual environment. For instructions on how to create and activate a virtual environment, see the 'Useful server commands' section below.

If your program runs from a `main.sh` file, you can easily automate the creation of the virtual environment by inserting the following script to your code. Substitute the name of the virtual environment and local package folder \(if applicable\) appropriately.

```text
virtualenv 'NAME_OF_THE_ENV'
. 'NAME_OF_THE_ENV'/bin/activate
pip install -r requirements.txt
pip install -f 'LOCAL_PACKAGE_FOLDER'/ -r requirements-local.txt

YOUR_CODE

deactivate
```

To create a virtualenv and use it permanently, one can use [mkvirtualenv](https://virtualenvwrapper.readthedocs.io/en/latest/):

```text
$ mkvirtualenv pandas
(pandas) $ pip install pandas
(pandas) $ python
(pandas) $ deactivate
$ echo "I am no longer in a virtualenv."
$ workon pandas
(pandas) $ pip install jupyter
```

### Parallelization

Parallelization refers to the spreading the code processing work across multiple cores \(CPUs\). Parallelization is useful to fasten the running time of codes by optimizing the available resources.

For a short introduction on parallelization in Python, please visit the following website: [https://sebastianraschka.com/Articles/2014\_multiprocessing.html](https://sebastianraschka.com/Articles/2014_multiprocessing.html)

### STATA

You can access the STATA program with graphical user interface on the haflinger. You can start Stata from VNC bash command line by the help of `xstata-mp` command.

### Python

The servers run both python2 and python3. You can access them by typing `python2` for python2 \(current version 2.7.18\) and `python` for python3 \(current version 3.8.5\). To leave the python shell and return to the system shell, type the python command `exit()`.

## Useful server commands:

| Code | Short description |
| :--- | :--- |
| `htop` | Check servers usage, CPU and memory \(see also `top`\) |
| `screen` | Create screen running in the background. Ctrl-A-D to close the screen, Ctrl-D to shut down \(terminate\) the screen. |
| `screen -r "number_of_screen` | Open previously created screen |
| `virtualenv “name_of_virtualenv” -p python` | Create a Python3 virtual environment |
| `virtualenv “name_of_virtualenv” -p python2` | Create a Python2 virtual environment |
| `. [‘name_of_virtualenv’]/bin/activate` | Activate virtual environment |
| `pip3 install -r requirements.txt` | Install requirements for virtual environment listed in requirements.txt |
| `pip3 install -r requirements_packages.txt -f packages/` | Install every requirement that are contained in a folder \(as files\) |
| `pip freeze` | Show downloaded python libraries |
| `pip freeze -> requirements.txt` | Export the currently downloaded python packages to a text file |
| `exit` | Terminate connection with the server |

## Debugging

In case you cannot create new virtualenvs or there is any other issue that might be related to reaching your home folder quota, here is how you can debug it:

1. Check if it is really a quota problem. Type

   ```text
   du -sh ./
   ```

   If it gives you anything close to 500M, you are proably dealing with a quota issue.

2. Clean up your home folder. In case you have any work in your home folder \(you shouldn't!\) move it somewhere else. Close any running screens and applications then delete all files in your cache: `find ~/.cache/ -type f -delete`
3. Check if the quota problem persists. If yes, let me know. If not, you need to take one last step so that it doesn't fill up again.
4. Set your cache folder to your sandbox. Add the following to your `~/.bashrc` file \(switch user with your username\):

   ```text
   export STATATMP
   export TMPDIR='/srv/sandbox/user/'
   export XDG_CACHE_HOME='/srv/sandbox/user'
   export PIP_REQUIRE_VIRTUALENV=true
   ```

5. If you use other shells \(e.g. fish\) you need to configure them as well. Fish users need to add the following to `~/.config/fish/config.fish` \(if the file does not exist, you need to create it, switch user with your username\):

```text
set -x STATATMP "/srv/sandbox/user"
set -x TMPDIR '/srv/sandbox/user'
set -x XDG_CACHE_HOME '/srv/sandbox/user'
set -x PIP_REQUIRE_VIRTUALENV true
```

For any other shell consult it's documentation on setting environment variables.

### Vncrestart

When using VNC, you might experience bad behavior after a while \(windows cannot be resized or the appications freeze etc\). For the resolution of the above problem, you can use the `vncrestart` command from SSH or VNC terminal which will reset your VNC within 60 seconds.

Important: Please keep in mind that resetting VNC will close all your opened applications and delete all your unsaved data.

## Contacts

* If you have technical difficulties with the server, please contact a Project Manager.
* For VPN-related problems, please contact CEU HelpDesk at helprequest@ceu.hu.
* If you have a problem with a specific application \(e.g., Stata, Matlab\), a Project Manager can help you decide whether the problem is with the operating system or with the application. In the latter case, he can put you in touch with Stata or Matlab support.

