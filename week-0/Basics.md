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
