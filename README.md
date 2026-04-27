*This project has been created as part of the 42 curriculum by aunoguei.*

# Born2beRoot

## Project description

**Born2beRoot** is a system administration project from 42 that consists of setting up a secure Linux server inside a virtual machine, following strict rules.

The goal is to understand:

- Virtualization
- Linux system administration
- Disk partitioning (LVM)
- Security policies (SSH, firewall, password rules)
- User and privilege management

## Virtualization 

**What is a Hypervisor?**

A hypervisor allows you to run multiple operating systems on a single physical machine.

I used VirtualBox, which is a **type 2 Hypervisor** (Hosted)
- Runs on top of a host OS
- Creates virtual machines (guests)

**How it works:**

The host OS provides hardware resources
The guest OS uses these resources as if they were its own

**Advantages of Virtual Machines**
- Cost-effective (no extra hardware)
- Easy backup and snapshots
- Safer testing environment
- Isolation between systems
- Reduced physical space

***VirtualBox vs UTM***

| Feature     | VirtualBox            | UTM                         |
| ----------- | --------------------- | --------------------------- |
| Platform    | Windows, Linux, macOS | macOS (Apple Silicon focus) |
| Performance | Good                  | Optimized for ARM           |
| Ease of use | Moderate              | Very easy                   |
| Flexibility | High                  | Medium                      |

## Operating system choice

I chose Debian as the operating system.

**What is Debian?**

Debian is a free and open-source Linux distribution based on the Linux kernel and GNU tools.

**Advantages**
- Stable and reliable
- Strong security support
- Large community
- Wide hardware compatibility
- Base for many distributions (Ubuntu, Tails, etc.)

**Disadvantages**
- Older packages (less bleeding-edge)
- Less beginner-friendly than some alternatives

***Debian vs Rocky Linux***

| Feature         | Debian          | Rocky Linux             |
| --------------- | --------------- | ----------------------- |
| Base            | Independent     | RHEL clone              |
| Stability       | Very high       | Enterprise-grade        |
| Package manager | APT             | DNF/YUM                 |
| Use case        | General purpose | Enterprise environments |

**Package management**

APT is the default package manager used in Debian.

Common commands:

```
sudo apt update
sudo apt install <package>
sudo apt remove <package>
```

***Apt vs Aptitude***

| Feature             | apt          | aptitude               |
| ------------------- | ------------ | ---------------------- |
| Default in Debian   | Yes          | No                     |
| Interface           | Command-line | CLI + interactive mode |
| Dependency handling | Basic        | More advanced          |
| Ease of use         | Simple       | Slightly more complex  |

## Main design choices

### Network configuration

There are several networking modes:

- **NAT (Network Address Translation)**
    - Default mode
    - VM accesses internet through host
    - Safer, isolated
- **Bridged**
    - VM appears as a device on the network
    - Has its own IP
- **Host-Only**
    - Communication only with host
    - No internet access

For this project, NAT is typically used for simplicity and security. It map a private insight IP address to a public outside IP address.

### Partitioning

**My Setup**

- 2 encrypted partitions
- Managed using LVM

**Why Encryption?**

- Protects sensitive data
- Prevents unauthorized access if disk is compromised

**LVM (Logical Volume Manager)**

LVM allows flexible disk management:
- Resize partitions dynamically
- Combine multiple disks (a single partition can reside on more than one physical hard disk)
- Snapshots support

### Security

**SSH**

Secure Shell is a protocol that allows secure remote access to the server.

- Uses encryption
- Client-server architecture
- Configured to run on port 4242

In this project, I used **OpenSSH Server**, which is the implementation of the SSH protocol installed on the machine.

OpenSSH Server:
- Handles incoming SSH connections
- Authenticates users
- Encrypts all communication between client and server

Key settings:
- Custom port (4242)
- Root login disabled for security

---

**UFW (Uncomplicated Firewall)**

UFW is a simple firewall interface for managing iptables.

Functions:
- Control incoming/outgoing traffic
- Block unauthorized access

***UFW vs firewalld***:

| Feature     | UFW           | firewalld        |
| ----------- | ------------- | ---------------- |
| Ease of use | Very easy     | Moderate         |
| Backend     | iptables      | nftables         |
| Use case    | Simple setups | Advanced systems |

---

**AppArmor**

AppArmor is a Mandatory Access Control (MAC) system.

- Restrict the actions that processes can perform.
- Protects system resources
- Enabled by default in Debian

| Feature    | AppArmor      | SELinux       |
| ---------- | ------------- | ------------- |
| Complexity | Easier        | More complex  |
| Control    | Path-based    | Context-based |
| Usage      | Debian/Ubuntu | RHEL/CentOS   |

---

**Password policy:**

A password policy is a set of rules designed to improve security by encouraging users to use strong passwords and handle them properly.

To enforce this policy, I configured the **libpam-pwquality** module:
- Password expires every 30 days
- Minimum 2 days between changes
- Warning 7 days before expiration
- Minimum 10 characters
- Must include:
    - Uppercase
    - Lowercase
    - Number
- No more than 3 identical consecutive characters
- Must not contain username
- at least 7 characters must differ from previous password 

### User management

**Sudo Configuration**

Sudo allows a permitted user to execute commands as another user (by default, the superuser).

The configuration is defined in:```/etc/sudoers```

I edited it safely using:```sudo visudo -f /etc/sudoers.d/sudo_config```

Rules applied:
- Maximum 3 attempts for authentication
- Custom error message on failure
- All sudo actions are logged:
    - Directory: /var/log/sudo/
- TTY mode enabled
- Restricted secure paths:
```/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin```

---

**Users**
- Root user
- A standard user with login name
- User added to sudo group and user42

### Installed services 
- OpenSSH Server
- UFW
- Sudo
- AppArmor


## Resources

- Debian documentation
- 42 guide (Born2beRoot)
- Linux security documentation
- YouTube tutorials

### Links:

https://wiki.debian.org/LVM#Overview  
https://wiki.debian.org/DebianInstall  
https://www.sudo.ws/about/intro/  
https://wiki.debian.org/AppArmor/HowToUse  
https://debian-handbook.info/browse/es-ES/stable/sect.apparmor.html  
https://debian-handbook.info/browse/es-ES/stable/security.html  
https://www.youtube.com/watch?v=wX75Z-4MEoM  
https://42-cursus.gitbook.io/guide/1-rank-01/born2beroot/whats-a-virtual-machine  
https://www.youtube.com/watch?v=Fhdxk4bmJCs  

### AI usage
- Understand new concepts
- Improve documentation clarity