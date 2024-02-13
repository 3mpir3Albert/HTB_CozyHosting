# Write Up
## Introduction
Before starting to present the way it has been taken to solve CozyHosting machine, I will present an outline with the concepts that will be used to solve challenge:
- Exhaustive enumeration of the website.
- Session Hijacking.
- Command injection.
- Hash cracking with Hascat.
- Abuse of ssh SUID configuration.
## Reconnaisance phase
As usual, reconnaissance phase was the first phase executed due to the fact that a hacker needs to know as much information about his target as possible before starting to exploit posible vulnerabilities.

To do this, you need to add a new line in the ''/etc/hosts'' file, where you define which IP is linked to which domain name. As shown in the following image, if you want to access CozyHosting.htb, you are going to send a request to 10.10.11.230 really:

[![hosts.png](https://i.postimg.cc/x1P6MnhB/hosts.png)](https://postimg.cc/WdhmPL27)

Next, Nmap is used to scan all ports on the server. The tool output is as follows:

[![Nmap.png](https://i.postimg.cc/5yRVx8qq/Nmap.png)](https://postimg.cc/GTGNK8fH)

As shown in the last image, two open ports were discovered exposing two different services:
- Port 22: SSH service.
- Port 80: HTTP service.

Due to the fairly up-to-date version of SSH, it seems that the way to go is to perform a technology scan on port 80.
