# Bypassing Client-Side Controls

## Table of Contents
0. [Server-Side and Client-Side Functionality](#server-side-and-client-side-functionality)
1. [Example of modifying HTTP Headers](#example-of-modifying-http-headers)
2. [Introduction to Client-Side Authentication with Javascript](#introduction-to-client-side-authentication-with-javascript)
3. [Resources](#resources)

## Server-Side and Client-Side Functionality 

### Server-Side Functionality 
In addition to the HTTP protocol used to send messages between client and server, web applications employ numerous technologies to deliver their functionality. When a user requests a dynamic resource, the server’s response is created on the fly, and each user may receive different content tailored for them. Dynamic content is generated by scripts or other code executing on the server.

Web applications employ a wide range of technologies on the server side to deliver their functionality:

Server-Side Technologies | Description
--- | ---
Scripting languages | <u><li>PHP</li><li>VBScript</li><li>Perl</li></u>
Web application platforms | <u><li>ASP.NET</li><li>Java</li></u>
Web servers | <u><li>Apache</li><li>IIS</li><li>Netscape Enterprise</li></u>
Databases | <u><li>MS-SQL</li><li>Oracle</li><li>MySQL</li></u>
Back-end components | <u><li>Filesystems</li><li>SOAP-based web services</li><li>Directory services</li></u>

### Client-Side Functionality 
For the server-side application to receive user input and actions and present the results to the user, it needs to provide a client-side user interface. Because all web applications are accessed via a web browser, these interfaces all share a common core of technologies.

Client-Side Technologies | Description
--- | ---
HTML | Tag-based language that is used to describe the structure of documents that are rendered within the browser.
Hyperlinks | A large amount of communication from client to server is driven by the user’s clicking on hyperlinks.
Forms | HTML forms are the usual mechanism for allowing users to enter arbitrary input via their browser.
CSS | Language used to describe the presentation of a document written in a markup language. Within web applications, it is used to specify how HTML content should be rendered on-screen.
JavaScript | Programming language that is used to extend web interfaces in which the client side is used not simply to submit user data and actions but also to perform actual processing of data. <u><li>I.e. Certain tasks can be carried out entirely on the client component, without needing to make a round trip of request and response to the server.</li></u>

### Example of Server and Client Interaction
![](https://i2.wp.com/wpshout.com/media/2014/08/client_server.png?w=1440&ssl=1)

## Example of modifying HTTP Headers

In this example, we would be introducing `curl`, a tool used to transfer data to or from a user without user interaction. On your linux machine, install curl if you haven't already done so:

`sudo apt-get install curl`

As discussed earlier, each line of a HTTP request contains headers. In the following exercise, we would be looking at the `Referer` header. Browsers include the `Referer` header within most HTTP requests. It is used to indicate the URL of the page from which the current request originated. Hence, it can be leveraged as a mechanism for transmitting data via the client.

#### Exercise 1

Connect to `http://natas4.natas.labs.overthewire.org` with the following credentials `natas4:Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ`.

After clicking on `Refresh Page`, you are displayed with the following message:

`Access disallowed. You are visiting from "http://natas4.natas.labs.overthewire.org/index.php" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/" `

The message gave as a clue that if we are referred from "http://natas5.natas.labs.overthewire.org/", we might be allowed access.

Let us try to edit the referer header of the HTTP request so that we are referred from "http://natas5.natas.labs.overthewire.org/". The command looks like this.

`curl -u natas4:Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ "http://natas4.natas.labs.overthewire.org" --referer "http://natas5.natas.labs.overthewire.org/"`

##### Why?
Because the user controls every aspect of every request, including the HTTP headers, they can easily change the value of the `Referer` header to the value that the application requires.

#### Exercise 2

Connect to `http://natas5.natas.labs.overthewire.org` with the the username natas5 and the password you have just obtained.

You are presented with the following message:

`Access disallowed. You are not logged in`

After looking at the page source, nothing seems interesting. Let us look at the HTTP Request/Response. There are 2 ways to go about this.

1. Right click > Inspect > Network > Click on your target of interest and look at the Headers Tab
2. `curl -v -u natas5:iX6IOfmpN7AYOQGPwtn3fXpbaJVJcHfq http://natas5.natas.labs.overthewire.org` 

Note that curl -v shows the HTTP request starting with `>` and HTTP response starting with `<`.

Looking at the `Set-Cookie` header, it shows `Set-Cookie: loggedin=0`. What if we change the cookie value to `1`? The command using `curl` looks like this:

`curl -u natas5:iX6IOfmpN7AYOQGPwtn3fXpbaJVJcHfq http://natas5.natas.labs.overthewire.org --cookie loggedin=1`

This further shows that the client can easily change HTTP headers to bypass controls.

## Introduction to Client-Side Authentication with Javascript

Client-side authentication is when authentication checks are performed completely at users’ side. The idea is that the authentication procedures, methods, or codes are delivered to the client, where they are executed to determine whether a user has access. 

### Exercise 1

Connect to `https://net-force.nl/challenge/level101/`. Upon clicking on `Secret Webpage`, we are prompted to key in the correct credentials. However, we cannot view the page source of `secret.html`, a handy trick would be to prepend `view-source:` to the start of the url. The page source shows us the password.

### Exercise 2

Connect to `https://net-force.nl/challenge/level102/`. Upon viewing the page source, it shows the JavaScript function that tests your input for the password. Our interest lies in the javascript function:

![alt text](https://github.com/tanhengyeow/lrn2lrn/blob/master/lessons/hengyeow-yoshi_1_web/src/javascript%20example.png)

It seems that the script is checking the user input with a generated password (the steps are shown in the function).

We can use an [online javascript compiler] (http://js.do/) and tweak the javascript to show us what the generated password of the javascript is. Note that adding `alert(password)` shows us the output of the password variable.

![alt text](https://github.com/tanhengyeow/lrn2lrn/blob/master/lessons/hengyeow-yoshi_1_web/src/javascript%20compiler.png)


## Resources
1. The Web Application Hacker's Handbook: Finding and Exploiting Security Flaws, 2nd Edition
2. [Understanding “Server-Side” and “Client-Side” in WordPress] (https://wpshout.com/understanding-server-side-client-side-wordpress/)
3. [OverTheWire] (http://overthewire.org/wargames/natas/)
4. [NetForce Challenges] (https://net-force.nl/challenges/)
5. [Pitfalls of Client-Side Authentication] (http://resources.infosecinstitute.com/the-pitfalls-of-client-side-authentication-solutions-to-net-force-javascript-ctf-challenges/#gref)
