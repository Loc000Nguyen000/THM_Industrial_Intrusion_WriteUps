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
<img width="652" height="636" alt="image" src="https://github.com/user-attachments/assets/6691019e-d82e-4e09-ac8d-e2e75e5f4a42" />

--> Decode Plan of Fabiano --> We have credential `fabiano:o3jVTktarGQI07q`
+ Log in ssh --> Get the flag user.txt
+ Scan `Linpeas` we've found the vector to priv

<img width="906" height="197" alt="image" src="https://github.com/user-attachments/assets/c454400a-7e45-4ff1-8240-89e607eec5d5" />

+ Get the flag root.txt

<img width="920" height="339" alt="image" src="https://github.com/user-attachments/assets/cf9b27ad-37cc-4485-96ec-26a6fc58344b" />

### Task 16:
+ `Nmap` scan only show 2 services up, web and ssh:

<img width="935" height="272" alt="image" src="https://github.com/user-attachments/assets/cf057757-6d40-4453-aeac-b16b17276251" />

+ Enumerating for dirs we found a keys directory:

<img width="855" height="725" alt="image" src="https://github.com/user-attachments/assets/a143388d-55dc-4c2d-b4cf-490a355dfe64" />
<img width="609" height="890" alt="image" src="https://github.com/user-attachments/assets/bc02f790-e0fb-4ee5-a629-f9832c0c537d" />

--> key_09 is a private ssh key.

<img width="542" height="606" alt="image" src="https://github.com/user-attachments/assets/56a81b4a-fd54-4f70-9e43-b135d945b908" />

+ We can use it to connect and get the user flag

<img width="624" height="586" alt="image" src="https://github.com/user-attachments/assets/f9a6bc3b-04e4-4f3f-ae85-525dbb0ae568" />

then I linpeased the box, useless in fact, sudo -l give us the answer we need

<img width="850" height="118" alt="image" src="https://github.com/user-attachments/assets/f4428b0c-416e-4761-b995-53473e94e1d0" />

+ by looking on gtfobins we found the exploit for vi with sudo and get root flag 

<img width="824" height="315" alt="image" src="https://github.com/user-attachments/assets/408a4ded-ab82-4f0c-a48d-c8320f3107fa" />
