A technology that creates a secure, encrypted connection (a "tunnel") between a remote device and a private network over a public network like the internet.

A VPN assigns an internal IP address to the remote device, allowing it to access resources on the private network as if it were physically there.
## Essential Components

|**Requirement**|**Description**|
|---|---|
|`VPN Client`|This is installed on the remote device and is used to establish and maintain a VPN connection with the VPN server. For example, this could be an OpenVPN client.|
|`VPN Server`|This is a computer or network device responsible for accepting VPN connections from VPN clients and routing traffic between the VPN clients and the private network.|
|`Encryption`|VPN connections are encrypted using a variety of encryption algorithms and protocols, such as AES and IPsec, to secure the connection and protect the transmitted data.|
|`Authentication`|The VPN server and client must authenticate each other using a shared secret, certificate, or another authentication method to establish a secure connection.|
  

> [!info]
> The primary use for a VPN is to give employees and administrators secure remote access to a company's internal resources (e.g., file servers, email) from anywhere. This provides security by encrypting the connection and is often more cost-effective than dedicated physical lines.

A VPN is only secure if the `Authentication` is strong. Only authorized users who can prove their identity should be allowed to connect to the VPN server.

## IPsec
[Internet Protocol Security](https://www.cloudflare.com/learning/network-layer/what-is-ipsec/) (`IPsec`) is a network security protocol that provides encryption and authentication for internet communications. It is a powerful and widely-used security protocol that provides encryption and authentication for internet communications and works by encrypting the data payload of each IP packet and adding an `authentication header` (`AH`), which is used to verify the integrity and authenticity of the packet. IPsec uses a combination of two protocols to provide encryption and authentication:

1. [Authentication Header](https://www.ibm.com/docs/en/i/7.1?topic=protocols-authentication-header) (`AH`): This protocol provides integrity and authenticity for IP packets but does not provide encryption. It adds an authentication header to each IP packet, which contains a cryptographic checksum that can be used to verify that the packet has not been tampered with.
2. [Encapsulating Security Payload](https://www.ibm.com/docs/en/i/7.4?topic=protocols-encapsulating-security-payload) (`ESP`): This protocol provides encryption and optional authentication for IP packets. It encrypts the data payload of each IP packet and optionally adds an authentication header, similar to AH.

IPsec can be used in two modes.

|**Mode**|**Description**|
|---|---|
|`Transport Mode`|In this mode, IPsec encrypts and authenticates the data payload of each IP packet but does not encrypt the IP header. This is typically used to secure end-to-end communication between two hosts.|
|`Tunnel Mode`|With this mode, IPsec encrypts and authenticates the entire IP packet, including the IP header. This is typically used to create a VPN tunnel between two networks.|

The following protocols are necessary to provide more security and encryption in a IPsec VPN

| **Protocol**                             | **Port**                                  | **Description**                                                                                                                                                                                                                                                                                                      |
| ---------------------------------------- | ----------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Internet Protocol` (`IP`)               | IP protocol numbers `50–51` (`ESP`, `AH`) | This is the primary protocol that provides the foundation for all internet communication. It is used to route packets of data between the VPN client and the VPN server. In the context of IPsec, protocol numbers 50 (ESP) and 51 (AH) identify VPN-related headers used between the VPN client and the VPN server. |
| `Internet Key Exchange` (`IKE`)          | `UDP/500`                                 | IKE is a protocol that is used to establish and maintain secure communication between the VPN client and the VPN server. It is based on the Diffie-Hellman key exchange algorithm, and it is used to negotiate and establish shared secret keys that can be used to encrypt and decrypt the VPN traffic.             |
| `Encapsulating Security Payload` (`ESP`) | IP protocol `50` _(NAT-T: `UDP/4500`)_    | ESP is also a protocol that provides encryption and authentication for IP datagrams. It is used to encrypt the VPN traffic between the VPN client and the VPN server, using the keys that were negotiated with IKE, either directly as IP protocol 50 or encapsulated in UDP/4500 when NAT traversal is used.        |
