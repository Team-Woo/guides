# Hosting a vanilla Minecraft server

## Introduction
In this guide, you will be able to host a Minecraft server as well as keeping it alive without having to be logged in all the time. Before you start, make sure you do meet the requirements and have a basic understanding of Linux, if you have issues or are stuck on a step you can ask for help in our [support server.](https://discord.gg/jcKEyxn)

## Requirements 
* You have a Linux VPS (Debian/Ubuntu).
* You are logged in as root or have a user that can use sudo.
* You know how to establish an SSH connection or use an SSH client.
<br/>

*If you do not know how to connect to your server I would recommend using an SSH client like [bitvise](https://www.bitvise.com/ssh-client-download) or [Putty](https://www.putty.org/) and ask for help in our [support server.](https://discord.gg/jcKEyxn)*

## Creating a user for Minecraft
For security purposes, Minecraft should not be running under the root user. We will create a new system user and group with home directory /opt/Minecraft that will run the Minecraft server:

```
sudo useradd -r -m -U -d /opt/Minecraft -s /bin/bash Minecraft
sudo apt install screen unzip -y
```
We are not going to set a password for this user. This is a good security practice because this user will not be able to log in via SSH. To login to the Minecraft user, you’ll need to be logged in to the server as root or user with sudo privileges.

Before starting with the installation process, make sure you switch to the Minecraft user.

```
sudo su - Minecraft
```

## Creating the Directory 
If you plan to have multiple versions of Minecraft running I would recommend creating a folder for them to make sure the files do not conflict.
```
mkdir Vanilla
cd Vanilla
```

## Installing Java
Run the commands below on the Minecraft user we created above.
```
curl -sL https://github.com/shyiko/jabba/raw/master/install.sh | bash && . ~/.jabba/jabba.sh
jabba install openjdk@1.14.0
jabba alias default openjdk@1.14.0
```

!!! Information 
    Run `jabba` to see if was installed correctly. 

## Getting the server jar
Please download one of the server Jars from below. Currently, 1.15.2 is the latest server jar.
1.15.2
```
wget https://launcher.mojang.com/v1/objects/bb2b6b1aefcd70dfd1892149ac3a215f6c636b07/server.jar -O server.jar
```
1.15.1
```
wget https://launcher.mojang.com/v1/objects/4d1826eebac84847c71a77f9349cc22afd0cf0a1/server.jar -O server.jar
```
1.15
```
wget https://launcher.mojang.com/v1/objects/e9f105b3c5c7e85c7b445249a93362a22f62442d/server.jar -O server.jar
```
1.14.4
```
wget https://launcher.mojang.com/v1/objects/3dc3d84a581f14691199cf6831b71ed1296a9fdf/server.jar -O server.jar
```

!!! information
    Don't see the version you are looking for? you can grab the server jar from this [website](https://mcversions.net/)

## Running the server
First, make sure you do have java installed by running `java` and make sure you have the server jar file by running `ls` and you should see `server.jar` or the file you downloaded, to run the server run the command below once.
```
java -Xmx1024M -Xms1024M -jar server.jar nogui
```
!!! Hint
    server.jar might include numbering depending where you have downloaded your jar from, you can also increase the ram used by upping the Xmx and Xms flags.
You will get a message asking you to accept the EULA.
![EULA](https://i.imgur.com/HgyNGLQ.png)

To accept the EULA just edit eula.txt and change false to true.
```
nano eula.txt
```
Before:
![EULA_file](https://i.imgur.com/8EST1o0.png)
After:
![EULA_TRUE](https://i.imgur.com/vwia9Bo.png)

!!! note
    To exit from nano use Ctrl + X and Y to save the edited text.
Next you can run the same command again to start the server.
```
java -Xmx1024M -Xms1024M -jar server.jar nogui
```
![MC_1](https://i.imgur.com/jBAx1GP.png)
![MC_2](https://i.imgur.com/naswFjM.png)

## Connecting to the server
You should grab the IP of your server which can be found using the command below if you do not know it.

```
dig +short myip.opendns.com @resolver1.opendns.com
```
 Copy the IP and open Minecraft up, go to servers and click add a server and under `Server Address` put the server's IP in and click Done.
 ![MC_SERVER](https://i.imgur.com/Sv1gMft.png)

## Keeping your server alive
### Screen

Screen is one way of keeping your server running in the background without having to keep your SSH session open.

!!! warning
    `screen` does not boot on load or write logs to the disk automatically, reboots would kill the screen due to only being a virtual session.

To start your server with screen, first, make sure you have `screen` package installed.

#### Installation
You can install screen using the one-liner below:
```bash
sudo apt update && sudo apt install screen -y
```
!!! warning
    Only run if you do not have screen installed and make sure to use exit first to go back to being root after you have installed use `sudo su - minecraft` and proceed.
#### Usage
You can then start your server by using the command below:
```bash
screen -S Minecraft java -Xmx1024M -Xms1024M -jar server.jar nogui
```
This should create a session you can safely leave without fear of it shutting down when you leave, 
You can leave the screen via `CTRL+AD` from this session so your Server is still online when you leave.

You can re-attach to the running screen by running `screen -r Minecraft` and either issue commands or shutdown the server via `CTRL+C`.