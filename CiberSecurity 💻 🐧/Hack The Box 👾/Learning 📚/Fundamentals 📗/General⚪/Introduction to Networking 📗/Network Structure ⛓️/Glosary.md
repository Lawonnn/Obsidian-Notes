## Command and Control (C2)
This is the server infrastructure that an attacker uses to send commands to malware that is running on a compromised (infected) computer.
### Analogy
Imagine a drone is the malware. The **C2** is the remote control the pilot uses to tell the drone where to go and what to do (e.g., "take pictures," "drop a package"). The malware on a computer is useless unless it can receive orders from its C2 server.
### Why is important
The module "[[Proxies]]" explains that if malware is not `proxy aware`, it cannot send its traffic through the company's proxy. This means it can't reach its C2 server on the internet to get commands, effectively neutralizing the threat.

## Proxy Aware
A piece of software is "proxy aware" if it is designed to detect and use a proxy server when it's required.
### Analogy
Imagine you're in a foreign country and don't speak the language. To order food, you need a translator (the proxy).
    - A **`proxy aware`** person knows they need a translator and will talk to the translator to get their food order to the chef.
    - A person who is **not** `proxy aware` will try to talk directly to the chef in their own language, and the chef won't understand. The order fails.
### Why is important
The module "[[Proxies]]" state that browsers like Chrome are automatically proxy aware on Windows. But malware might not be. If the malware isn't proxy aware, the forward proxy acts as a simple but effective defense, blocking the malware's communication.

## Winsock
WinSock is the standard programming interface (API) that applications on Windows use to handle network communications (like connecting to the internet). It's built directly into the Windows operating system.

## libcurl
Is a very popular, open-source library that programmers use to make network requests. Unlike `WinSock`, it is **not** part of the Windows operating system. Applications like Firefox bundle it to handle their own web traffic.