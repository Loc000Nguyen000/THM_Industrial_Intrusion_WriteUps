### Task 5: OSINT1
+ Found 2 subdomain:
```
https://stage0.virelia-water.it.com/
```
![alt text](image.png)

+ Use `Magic` Decoding Recipe on CyberChef https://gchq.github.io/CyberChef/

--> Get the flag: `THM{Su5sss}`

***Note:Magic on the subdomain without ip, the one with the numbers***

### Task 6: OSINT2
+ From stage0.virelia-water.it.com we find a link to a script init.js on a github
![alt text](image-1.png)

+ The script himself give us a new clue with a subdomain
![alt text](image-2.png)

+ By looking on dnsdumpster we find a base64 encode txt record
![alt text](image-3.png)

+ And we got the flag
![alt text](image-4.png)

### Task 7: OSINT3
+ "After the initial breach, a single OT-Alert appeared in Virelia’s monthly digest—an otherwise unremarkable maintenance notice, mysteriously signed with PGP. Corporate auditors quietly removed the report days later, fearing it might be malicious. Your mission is to uncover more information about this mysterious signed PGP maintenance message."

+ Checked the OT-Alerts page, the report was clearly removed:
![alt text](image-5.png)
![alt text](image-6.png)

+ Then I did an OSINT on the website: backend, available folders, etc. Found that it was a hosted GitHub page (Wappalyzer might have told me this is I was on my other machine):
![alt text](image-7.png)

+ So I go to their GitHub and lookup the commit history, find the deleted OT-Alert:
![alt text](image-8.png)
![alt text](image-9.png)

+ I copied the bit from `-----BEGIN PGP SIGNED MESSAGE-----` up to `-----END PGP SIGNATURE-----` and saved it in an .asc file. Which can be examined with gpg.

+ gpg -vv --verify message.asc showed me the 16 hex digit key https://security.stackexchange.com/questions/62916/can-i-get-a-public-key-from-pgp-signature signature packet `(signing): algo 1 (SHA 1)` 
`digest: algo 10 (SHA 512)` issuer key ID, which I used next examined the key with a public keyserver:
`gpg --keyserver keyserver.ubuntu.com --recv-keys A4F0FEB084A311E5 `

```       
gpg: key F8ED5BC28874364F: public key "Ghost (THM{h0pe_th1s_k3y_doesnt_le4d_t0_m3}) <solstice.tech.ops@gmail.com>" imported gpg: Total number processed: 1 
gpg:       imported: 1
```
+ Even better:
`gpg --list-keys --with-fingerprint --with-subkey-fingerprint A4F0FEB084A311E5  
` 

```
pub   rsa2048 2025-06-23 [SCEA]
      C9D5 2FA5 AC32 05AF ED0C  B242 F8ED 5BC2 8874 364F
uid           [ unknown] Ghost (THM{h0pe_th1s_k3y_doesnt_le4d_t0_m3}) <solstice.tech.ops@gmail.com>
uid           [ unknown] DarkPulse (THM{h0pe_th1s_k3y_doesnt_le4d_t0_m3}) <alerts@virelia-water.it.com>
sub   rsa2048 2025-06-23 [SEA] [expires: 2025-12-20]
      88DE DE7B 7305 13BD 5EDD  6D9F A4F0 FEB0 84A3 11E5
```