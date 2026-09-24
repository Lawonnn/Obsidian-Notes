There are many types of proxies, but the key ones are:

## Dedicated Proxy / Forward Proxy
A server that acts as an intermediary for a client's **outgoing** requests to the internet. The client sends its request to the proxy, and the proxy forwards it to the destination server. To the destination server, the traffic appears to come from the proxy, not the original client.

- **Proxy Awareness:** The ability of software (like malware) to detect and correctly use a proxy. If a program is not proxy-aware, it will try to connect directly to the internet and be blocked by the proxy.

**Practical Application:**

- **Corporate Defense:** Companies use forward proxies as a web filter to block access to malicious websites and control what resources employees can access. It's a strong defense against malware that is not proxy-aware, as it prevents the malware from communicating with its Command and Control (C2) server.
- **Penetration Testing:** Security professionals use tools like Burp Suite, which acts as a forward proxy to intercept, inspect, and modify web traffic for testing purposes.

**Watch Out For:**

- Not all applications handle proxies the same way. Some, like Chrome, use the main operating system's proxy settings by default (`WinSock`), while others, like Firefox, have their own separate configuration (`libcurl`). This difference can be crucial when analyzing how malware might behave on a network.
![[Pasted image 20260724203443.png]]

## Reverse Proxy
- **Filtering Incoming Traffic:** A company can place a reverse proxy like **Cloudflare** in front of its web servers. The proxy can absorb massive DDoS attacks or block known malicious requests before they ever reach the company's actual server.
- **Load Balancing:** If a website is very popular, it might have many servers. The reverse proxy can distribute incoming requests evenly across all the servers so no single one gets overloaded. (This concept is related but not detailed in this specific section).
- **Pivoting for Pentesters:** An attacker who has compromised one machine can set up a reverse proxy on it. This allows them to channel their traffic _through_ the infected machine to attack other computers on the internal network, bypassing firewalls and hiding their activity.
![[Pasted image 20260724205119.png]]
In short, a reverse proxy accepts requests on behalf of a server, filters them, and then forwards them to the correct destination.

## (Non-) Transparent Proxy
As the name suggests, a transparent proxy is **invisible** to the client (your computer).

- **How it works:** You don't configure anything on your computer or in your browser. The network itself is set up to automatically redirect all your web traffic through the proxy server. Your computer _thinks_ it's talking directly to the internet, but the proxy intercepts and manages the connection without you knowing.

Non-Transparent Proxy is the opposite. The proxy is **visible**, and the client _must_ be configured to use it.

- **How it works:** You have to go into your operating system's network settings or your browser's settings and manually enter the proxy server's IP address and port number. If you don't do this, your internet traffic will be blocked because the network rules only allow connections that go through the proxy.