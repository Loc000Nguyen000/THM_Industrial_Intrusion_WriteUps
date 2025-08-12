### Task 15:
+ Scan ports:
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIK+DHfy34tiXu4/Xqc08zwLgS3RHagBAWYiYevvhCfIz
79/tcp open  finger  syn-ack ttl 63 Linux fingerd
|_finger: No one logged on.\x0D
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52 ((Ubuntu))
|_http-title: PrecisionChess IoT - Smart Chessboard Control
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)
```
+ Scan directories with nothing so we access port 80
--> We have 3 names Magnus, Fabiano, Hikaru
+ We have port 79 --> service `Finger` 
+ Run Finger with 3 names we found:

--> Decode Plan of Fabiano --> We have credential `fabiano:o3jVTktarGQI07q`
+ Log in ssh --> Get the flag user.txt
+ Scan `Linpeas` we've found the vector to priv


+ Get the flag root.txt



### Task 16:
+ `Nmap` scan only show 2 services up, web and ssh:


+ Enumerating for dirs we found a keys directory:


--> key_09 is a private ssh key.

+ We can use it to connect and get the user flag


then I linpeased the box, useless in fact, sudo -l give us the answer we need


+ by looking on gtfobins we found the exploit for vi with sudo and get root flag 
