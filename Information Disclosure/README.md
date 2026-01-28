
## Lab Description :

![image](https://github.com/eeugenewyj/Portswigger_Labs/assets/Screenshot 2026-01-28 174433.png)

## Solution :

Click on any product in the website & monitor the request in burp. When we click on any product, a GET request is sent as follows.

```
GET /product?productId=1 HTTP/2
Host: 0ae2009304e0949c80fe30790077004c.web-security-academy.net
Cookie: session=nPkeTtVLJNw9Kts1OurcXpDreIZsyy6G
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0ae2009304e0949c80fe30790077004c.web-security-academy.net/
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
```

The above GET request has `?productTd=` parameter which is set to the id of the product (1).

We firstly tried `-1`, for boundary test. However, the application responds with a `Not Found` message, indicating that the product does not exist but no error is thrown.

We then tried `999999999999` a oversized numeric value to test for integer overflow.

We get a **verbose error message** in the response.

```
HTTP/2 500 Internal Server Error

Content-Length: 1693

Internal Server Error: java.lang.NumberFormatException: For input string: "999999999999"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
	at java.base/java.lang.Integer.parseInt(Integer.java:661)
	at java.base/java.lang.Integer.parseInt(Integer.java:777)
	at lab.c.w.x.y.Z(Unknown Source)
	at lab.o.go.g.z.h(Unknown Source)
	at lab.o.go.i.z.p.E(Unknown Source)
	at lab.o.go.i.e.lambda$handleSubRequest$0(Unknown Source)
	at s.x.s.t.lambda$null$3(Unknown Source)
	at s.x.s.t.N(Unknown Source)
	at s.x.s.t.lambda$uncheckedFunction$4(Unknown Source)
	at java.base/java.util.Optional.map(Optional.java:260)
	at lab.o.go.i.e.y(Unknown Source)
	at lab.server.k.a.n.l(Unknown Source)
	at lab.o.go.v.B(Unknown Source)
	at lab.o.go.v.l(Unknown Source)
	at lab.server.k.a.k.p.B(Unknown Source)
	at lab.server.k.a.k.b.lambda$handle$0(Unknown Source)
	at lab.c.t.z.p.Q(Unknown Source)
	at lab.server.k.a.k.b.Q(Unknown Source)
	at lab.server.k.a.r.V(Unknown Source)
	at s.x.s.t.lambda$null$3(Unknown Source)
	at s.x.s.t.N(Unknown Source)
	at s.x.s.t.lambda$uncheckedFunction$4(Unknown Source)
	at lab.server.gv.B(Unknown Source)
	at lab.server.k.a.r.G(Unknown Source)
	at lab.server.k.w.c.q(Unknown Source)
	at lab.server.k.q.m(Unknown Source)
	at lab.server.k.c.m(Unknown Source)
	at lab.server.gd.F(Unknown Source)
	at lab.server.gd.r(Unknown Source)
	at lab.x.e.lambda$consume$0(Unknown Source)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1144)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:642)
	at java.base/java.lang.Thread.run(Thread.java:1583)

Apache Struts 2 2.3.31
```
At the end of the response, we have the name & version of the software that is being used in the backend - `Apache Struts 2 2.3.31`

Quick google search reveals 23 vulerabilities associated with Apache Struts 2 2.3.31, implying that thats our answer.

Submit the answer to solve the lab.

![image](https://github.com/eeugenewyj/Portswigger_Labs/assets/Screenshot 2026-01-28 181355.png)

## Further findings :

The solution to this lab can be achieved in multiple ways. An attacker can supply different types of invalid input to the `productId` parameter, such as `strings`, `floating-point values`, `special characters`, or `overly large numbers`, all of which trigger the same verbose stack trace and reveal the vulnerable framework and its version.

