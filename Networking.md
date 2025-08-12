### Task 22:
Open taskfile in Wireshark; filter for modbus traffic:
`tcp.port == 502`

Follow TCP stream for Read Holding Registers
The flag is obvious, but can filter for outgoing traffic from exploited machine and tidy the answer.

<img width="1730" height="586" alt="image" src="https://github.com/user-attachments/assets/297acb11-02aa-42ec-9476-acae5ad70dd0" />

### Task 23:
+ Two ways to do this task
We could use the discovery.py script from the THM OT training room with a slight modification.
https://tryhackme.com/room/attackingics1

``` 
#!/usr/bin/env python3

import sys
import time
from pymodbus.client import ModbusTcpClient as ModbusClient
from pymodbus.exceptions import ConnectionException

ip = sys.argv[1]
client = ModbusClient(ip, port=502)
client.connect()
while True:
    for start in range(0, 200, 10):
        rr = client.read_holding_registers(start, count=10)
        print(rr.registers)
        time.sleep(1)
```
--> 'start' will begin at register 0 to 200 (although I confirmed there are only 125 registers) and will go by 10 registers at a time. We had Wireshark running on Tun0 or whatever connection is linked to the machine and captured the response traffic like the previous task.

<img width="1751" height="850" alt="image" src="https://github.com/user-attachments/assets/726eaccb-9f4d-4d20-8473-074a0f0f2251" />

+ 2nd Method will print output directly to terminal:
```
#!/usr/bin/env python3

from pymodbus.client import ModbusTcpClient
import struct

IP = "10.10.0.231"  # replace with your target
client = ModbusTcpClient(IP, port=502)
client.connect()

#### Try scanning first 200 registers
for start in range(0, 200, 10):  # step in chunks of 10
    result = client.read_holding_registers(start, count=10)
    if not result.isError():
        # Each register is 16 bits (2 bytes)
        data = b''.join(struct.pack('>H', r) for r in result.registers)

        # Convert to ASCII where printable
        ascii_output = ''.join(chr(b) if 32 <= b <= 126 else '.' for b in data)

        # Display results
        print(f"Registers {start:03}-{start+9:03}: {ascii_output}")

client.close()
```

<img width="321" height="342" alt="image" src="https://github.com/user-attachments/assets/1e957cca-b654-4c7e-b6e3-e48a0df3d50c" />
