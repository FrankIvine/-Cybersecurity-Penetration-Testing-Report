PENETRATION TESTING REPORT
Pentester Name (Cybersecurity Professional)	Frank Ivine Marara
Date	18 September 2026
Modules Completed	W2-PM1(Multiple Kali Tools)
                  W2-PM5 (Zenmap Scanning)
Client/Target	1.Networkwalks (secured written permission already)
              2.My own local LAN Network
Permission secured from client?	Yes
Phases covered	Phase 1: Reconnaissance & Footprinting 
                Phase 2: Scanning & Network Discovery
                Phase 3-5: in Progress
Environment:	Kali Linux in Oracle VirtualBox

Liability Disclaimer
This report is for authorized and educational purposes only. Unauthorized testing or exploitation of systems without consent may violate cybersecurity laws. The author assumes no liability for misuse or damages resulting from this information.

Introduction
The assessment reviewed DNS information, host discovery, network reconnaissance, and basic web server security indicators. The supplied screenshots show testing from Kali Linux using DNSRecon, nslookup, Nmap through Zenmap, and Nikto. The work focused on identifying exposed infrastructure, reachable hosts, DNS records, web server characteristics, and basic HTTP security-header issues.
Tools Used
Tool	Purpose	Key Findings
WHOIS	Domain registration details	Registrar: GoDaddy; Nameservers: HostGator; Expiry: 2027
DNSRecon	DNS enumeration	MX, TXT, and SRV records found; SPF and Google verification tokens
Nslookup	DNS resolution	Domain resolves to IP 192.232.216.135
Nikto	Web server vulnerability scan	Missing CSP, HSTS, and X Content Type Options headers
WAFW00F	WAF detection	ModSecurity (SpiderLabs) identified
Whatweb	Web technology fingerprinting	WordPress 7.1.1, Bootstrap 7.1.1, Apache server
Zenmap/Nmap	Network and port scanning	Active hosts: 10.0.0.1–10.0.0.3; Open ports: 135, 445, 7070
Ping Scan	Host discovery	3 hosts up in subnet 10.0.0.0/24

Activities Performed
a)  DNS Enumeration
An initial DNSRecon command produced an argument error, as shown in Evidence 1. A corrected DNSRecon run then completed successfully for networkwalks.com. The successful run identified two nameservers, an A record, an MX record, an SPF-related TXT record, and multiple SRV records for autodiscover.
•	A record: networkwalks.com resolved to 192.232.216.135.
•	Nameservers observed: ns6136.hostgator.com and ns6135.hostgator.com.
•	MX record observed: mail.networkwalks.com.
•	TXT record observed: an SPF policy containing ip4:50.87.144.87 and an include for websitewelcome.com.
•	SRV records observed for _autodiscover._tcp.networkwalks.com pointing to cpanelmaildiscovery.cpanel.net on port 443.
•	DNSRecon reported no answer for its DNSSEC query. This observation alone does not prove the complete DNSSEC configuration status.
b) DNS Lookup Verification
nslookup used the local resolver at 192.168.1.1 and returned networkwalks.com as 192.232.216.135. This independently confirms the address shown by the DNSRecon evidence.
c) Internal Host Discovery
Zenmap performed a Ping Scan against 10.0.0.0/24 using the Nmap command nmap -sn 10.0.0.0/24. The scan reported three hosts as up: 10.0.0.1, 10.0.0.2, and 10.0.0.3. The scan covered 256 IP addresses and completed in about 3.03 seconds.
d) Internal Network Reconnaissance
Zenmap also ran an Intense Scan against 10.0.0.0/24 using nmap -T4 -A -v 10.0.0.0/24. The evidence shows the scan processing the /24 range and reporting many addresses as down. The supplied screenshot does not show the complete service and port results for 10.0.0.1 through 10.0.0.3, so no port-level conclusion is made from this screenshot.
e) Web Server Reconnaissance
Nikto 2.6.0 targeted networkwalks.com at 192.232.216.135 on port 80. The server was identified as Apache, and the output included WordPress-related headers. Nikto also reported several missing recommended HTTP security headers and observed responses involving numerous archive-style paths.
•	Missing Content-Security-Policy header reported.
•	Missing Strict-Transport-Security header reported.
•	Missing X-Content-Type-Options header reported.
•	A WordPress-related response header was observed.
•	Nikto observed cookies being returned from several tested paths, including a __wpdm_client cookie.
•	The evidence does not establish successful access to backup files or sensitive data. The archive-style path responses should therefore be treated as an exposure indicator requiring manual verification.
Risk Analysis
The risk ratings below reflect the evidence supplied in the screenshots. They describe security exposure indicators rather than confirmed compromise.
Finding	Evidence	Risk	Potential Impact	Priority
Missing HTTP security headers	Nikto output	Medium	Reduced browser-side protection against selected web attacks and content-type abuse.	High
HTTP service identified on port 80	Nikto target and server response	Medium	Unencrypted HTTP traffic can expose sessions or content if HTTPS enforcement is incomplete.	High
Potentially exposed archive/backup-style paths	Nikto path responses	High if sensitive files exist	Accidental disclosure of configuration, source, backups, or other sensitive material.	High
DNS information disclosure	DNSRecon and nslookup	Low	Public DNS records reveal hosting, mail, and service infrastructure information.	Medium
Internal hosts discovered	Nmap Ping Scan	Low to Medium	Reachable internal assets provide a starting point for authorized internal security testing.	Medium
DNSSEC query returned no answer	DNSRecon	Informational	The screenshot does not provide enough evidence to confirm whether DNSSEC is absent or configured incorrectly.	Low

Recommendations
1.	Enforce HTTPS across the public website and redirect HTTP traffic to HTTPS.
2.	Enable HSTS after confirming HTTPS works correctly across the domain and required subdomains.
3.	Add Content-Security-Policy after testing the policy against the WordPress installation and required third-party resources.
4.	Add X-Content-Type-Options: nosniff.
5.	Review all paths reported by Nikto, especially archive, backup, compressed, certificate, database, and configuration-style filenames. Remove unnecessary files from the web root and block direct access to sensitive files.
6.	Review WordPress plugins, themes, and the WordPress core version. Keep all components patched and remove unused components.
7.	Review cookie settings and use Secure, HttpOnly, and appropriate SameSite attributes for session or sensitive cookies.
8.	Review DNS records and remove obsolete records. Confirm SPF, DKIM, and DMARC policies for mail security.
9.	Confirm the intended internal network segmentation. Restrict management services and unnecessary ports between VLANs or network segments.
10.	Repeat the assessment after remediation and retain before-and-after evidence.
Conclusion
The test confirmed that networkwalks.com is operational and protected by ModSecurity, but several configuration weaknesses exist. The supplied evidence shows a successful reconnaissance exercise covering DNS, public web infrastructure, and an internal /24 network. The public target resolved to 192.232.216.135, while the internal discovery scan identified three active hosts in the 10.0.0.0/24 range. Nikto identified Apache and WordPress-related indicators and reported missing HTTP security headers. The strongest follow-up item is a manual review of archive and backup-style paths because the screenshots show responses from those paths, while the evidence does not prove sensitive-file disclosure. The recommended remediation focuses on HTTPS enforcement, HTTP security headers, web-root hygiene, WordPress maintenance, cookie security, DNS hygiene, and internal network segmentation.
For screenshorts download the pdf document above
