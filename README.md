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

[![WhatWeb.png](https://i.postimg.cc/prjXB6k4/WhatWeb.png)](https://postimg.cc/649XWzkf)

As you can see, nginx 1.18.0 is running behind, but no important information is displayed. Therefore, the website was accessed to look for some useful information:

[![image.png](https://i.postimg.cc/xqr7KsFs/image.png)](https://postimg.cc/y3PvKTK9)

After searching for information, the only data retrieved is the login panel behind the path "/login". So a search was initiated using Wfuzz to list more paths and resources on the server.

```bash
wfuzz -c -t 200 --hc 404,400 --hh 0 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt http://CozyHosting.htb/FUZZ 
```

The following diagram represents the paths and resources found:

[![diagrama.png](https://i.postimg.cc/fRT8Ft0W/diagrama.png)](https://postimg.cc/svqPG2cb)

After finding these resources on the web, you can test some potential vulnerabilities, for example by bypassing the login panel with a SQLi or Type Juggling, etc. ..... But nothing worked.
If you find yourself in this situation and you have tested all possible vulnerabilities, it is likely that you have not done a thorough reconnaissance.  Therefore, Wfuzz was ran again with another dictionary:

```bash
wfuzz -c -t 200 --hc 404,400 --hh 0 -w /usr/share/SecLists/Discovery/Web-Content/combined_directories.txt http://CozyHosting.htb/FUZZ
```

With that dictionary, another route called "actuator" was found, if you go into it you will find more paths, as shown behind:

[![image.png](https://i.postimg.cc/DfgtjbC0/image.png)](https://postimg.cc/SjnTjjbb)
## Exploitation phase

A session ID can be found in the path "sessions". With this session ID under control it is possible to perform a session hijacking. So it was copied and pasted into the cookie that the website assigned to me.
