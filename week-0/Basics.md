# BASICS

## 1. What happens when you type https://www.salesforce.com?


You type:

https://www.salesforce.com

↓

Browser checks:

"Have I visited this recently?"

↓

If not...

Browser asks DNS:

"What's the IP address for www.salesforce.com?"

↓

DNS replies:

23.xx.xx.xx

↓

Browser opens a TCP connection.

↓

Browser performs a TLS/SSL handshake.

↓

Browser sends:

GET /

↓

Load Balancer receives the request.

↓

Application Server receives it.

↓

Application may query databases or other services.

↓

HTML, CSS, JavaScript are returned.

↓

Browser renders the page.

## 2. What is DNS?

DNS (Domain Name System) is the internet's phonebook. It translates human-readable domain names (like google.com) into numerical IP addresses (like 192.0.2.1) that computers use to route internet traffic. This eliminates the need to memorize long, complex strings of numbers to navigate the web.

## 3. What is the difference between HTTP and HTTPS?

HTTP (HyperText Transfer Protocol) is an application-layer protocol used for transferring data over the web. It was introduced by Tim Berners-Lee to enable communication between web browsers and web servers. HTTP defines a set of rules that allow clients (browsers) to request resources such as web pages, images, or videos from servers. 

Browser
   ↓
Internet
   ↓
Server

Key Points about HTTPKey:
Uses plain text for data transmission
Does not provide encryption or authentication
Vulnerable to data interception and modification
Default port number: 80

HTTPS stands for Hyper Text Transfer Protocol Secure. HTTP Secure (HTTPS), could be a combination of the Hypertext Transfer Protocol with the SSL/TLS convention to supply encrypted communication and secure distinguishing proof of an arranged web server. HTTPS is more secure than HTTP because HTTPS is certified by the SSL(Secure Socket Layer). Whatever website you are visiting on the internet, if its URL is HTTP, then that website is not secure.

Why HTTPS is Secure
Data is encrypted, preventing eavesdropping
Server identity is verified using digital certificates
Protects against man-in-the-middle attacks

src :https://www.geeksforgeeks.org/computer-networks/difference-between-http-and-https/

## 4. what is TCP

TCP (Transmission Control Protocol) is a protocol that allows devices to communicate reliably over a network. It ensures that data reaches the destination correctly and in the right order, even if parts of the network are slow or unreliable.

It works at the Transport Layer (Layer 4) of the OSI model(https://www.geeksforgeeks.org/computer-networks/open-systems-interconnection-model-osi/) and is an essential part of the TCP/IP protocol suite used for Internet communication.
TCP establishes a logical connection between the sender and receiver before data transmission begins.
It ensures that data is delivered accurately and in the same order in which it was sent using acknowledgements and sequence numbers.
TCP detects errors using checksums and retransmits lost or corrupted packets to maintain data integrity.
It controls the data transmission rate to avoid overwhelming the receiver and adapts to network congestion for efficient communication.



## 5, What is an API

An API (Application Programming Interface) is a set of rules that allows different software applications to communicate and exchange data with each other. It acts as a bridge between systems, enabling one application to request services or information from another in a structured way.

APIs help different applications connect and work together smoothly.
They allow data sharing and communication without exposing internal system logic.
APIs are widely used in web applications, mobile apps, payment systems, and cloud services.
Example: A mobile banking app requests account details through an API. The API verifies the request, fetches data from the banking server, and returns the account information to the app.


## 6. What is OAuth?

OAuth primary purpose is authorization—allowing one application to access resources in another on a user's behalf, without sharing the user's password.

## 7. What is the difference between authentication and authorization?

Authentication:
    Who are you?

Authorization:
    What are you allowed to do?

Example: You enter Salesforce.

Authentication:
    Username + MFA.
Authorization:
    Can you view Opportunities?
    Can you delete Accounts?
    Are you a System Administrator?

## 8. If Salesforce needs to call SAP, how does it communicate?

At High level
    Salesforce
        ↓
    REST API
        ↓
    HTTPS
        ↓
    OAuth
        ↓
    SAP API

## 9. What is a Load Balancer?

A load balancer is a networking device or software application that distributes and balances the incoming traffic among the servers to provide high availability, efficient utilization of servers and high performance.

Works as a "traffic cop" routing client requests across all servers.
Ensures that no single server bears too many requests, which helps improve the performance, reliability and availability of applications.
Highly used in cloud computing domains, data centers and large-scale web applications where traffic flow needs to be managed.

## 10. What is a CDN?

A content delivery network (CDN) is a geographically distributed group of servers that caches content close to end users. A CDN allows for the quick transfer of assets needed for loading Internet content, including HTML pages, JavaScript files, stylesheets, images, and videos.