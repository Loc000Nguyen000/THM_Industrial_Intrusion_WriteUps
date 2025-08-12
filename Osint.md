### Task 5: OSINT1
+ Found 2 subdomain:
```
https://stage0.virelia-water.it.com/
```
<img width="803" height="28" alt="image" src="https://github.com/user-attachments/assets/045a6100-1924-408f-a9bc-0d21f6cbbe1e" />

+ Use `Magic` Decoding Recipe on CyberChef https://gchq.github.io/CyberChef/

--> Get the flag: `THM{Su5sss}`

***Note:Magic on the subdomain without ip, the one with the numbers***

### Task 6: OSINT2
+ From stage0.virelia-water.it.com we find a link to a script init.js on a github
<img width="967" height="1044" alt="image-1" src="https://github.com/user-attachments/assets/ea7b2f60-7ae9-4569-9769-29aa584d8470" />

+ The script himself give us a new clue with a subdomain
<img width="1574" height="287" alt="image-2" src="https://github.com/user-attachments/assets/3902aa30-a7e4-40ae-b975-ea3fa60629b6" />

+ By looking on dnsdumpster we find a base64 encode txt record
<img width="1390" height="795" alt="image-3" src="https://github.com/user-attachments/assets/f2b48aa2-747f-479f-bd26-2bf7ecc224d6" />

+ And we got the flag
<img width="988" height="696" alt="image-4" src="https://github.com/user-attachments/assets/e641438c-af62-4e30-8631-86a0dcc9c416" />

### Task 7: OSINT3
+ "After the initial breach, a single OT-Alert appeared in Virelia’s monthly digest—an otherwise unremarkable maintenance notice, mysteriously signed with PGP. Corporate auditors quietly removed the report days later, fearing it might be malicious. Your mission is to uncover more information about this mysterious signed PGP maintenance message."

+ Checked the OT-Alerts page, the report was clearly removed:
<img width="1619" height="444" alt="image-5" src="https://github.com/user-attachments/assets/db772add-70c7-43be-b126-2b0f147c2929" />
<img width="1432" height="637" alt="image-6" src="https://github.com/user-attachments/assets/29f01f48-9c75-4463-b4ac-aae2ace1e591" />

+ Then I did an OSINT on the website: backend, available folders, etc. Found that it was a hosted GitHub page (Wappalyzer might have told me this is I was on my other machine):
<img width="872" height="513" alt="image-7" src="https://github.com/user-attachments/assets/d65a17c0-7279-4942-9e75-692be6e38b81" />

+ So I go to their GitHub and lookup the commit history, find the deleted OT-Alert:
<img width="1341" height="907" alt="image-8" src="https://github.com/user-attachments/assets/77359ef8-c166-4015-80e7-c22bf07bc348" />
<img width="1109" height="849" alt="image-9" src="https://github.com/user-attachments/assets/65e675e9-63af-4bd8-bc87-48bc8b87943e" />

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
