# HTTP Basic Auth
`Basic HTTP authentication` is a type of authentication handled directly by the webserver to protect a specific page/directory, without directly interacting with the web application. For example
![[Pasted image 20260901065649.png]]

Also we can tell if the website is using `Basic HTTP authentication` if in a HTTP response header contains `Basic realm=....`
```
┌──(kali㉿kali)-[~]
└─$ curl -i 154.57.164.78:32031

HTTP/1.1 401 Authorization Required
Date: Tue, 01 Sep 2026 09:59:00 GMT
Server: Apache/2.4.41 (Ubuntu)
Cache-Control: no-cache, must-revalidate, max-age=0
WWW-Authenticate: Basic realm="Access denied" <------ HERE
Content-Length: 13
Content-Type: text/html; charset=UTF-8
```

We have to ways to provide the credentials to a `Basic HTTP authentication`:
- Using the flag `-u` that enable us to type the `username:password`
```
curl -u admin:admin <url>
```

- Typing directly in the `url` the `username:password`
```
curl http://admin:admin@<url>
```

If we add a `-v` flag, we will see

```
┌──(kali㉿kali)-[~]
└─$ curl http://admin:admin@154.57.164.82:31397 -v

*   Trying 154.57.164.82:31397...
* Established connection to 154.57.164.82 (154.57.164.82 port 31397) from 10.0.2.15 port 42514 
* using HTTP/1.x
* Server auth using Basic with user 'admin'
> GET / HTTP/1.1
> Host: 154.57.164.82:31397
> Authorization: Basic YWRtaW46YWRtaW4= <---- HERE
> User-Agent: curl/8.21.0
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 200 OK
< Date: Wed, 02 Sep 2026 10:50:23 GMT
< Server: Apache/2.4.41 (Ubuntu)
< Cache-Control: no-cache, must-revalidate, max-age=0
< Vary: Accept-Encoding
< Content-Length: 1156
< Content-Type: text/html; charset=UTF-8

```

As we are using `Basic HTTP auth`, we see that our HTTP request sets the `Authorization` header to `Basic YWRtaW46YWRtaW4=`, which is the base64 encoded value of `admin:admin`. If we were using a modern method of authentication, like `JWT`, the `Authentication` would be of type `Bearer` and would contain a longer encrypted token


> [!TIP]
> With the flag `-H`, we can modify any header. We can add the `-H` flag multiple times to specify multiple headers
> 
> ```
> ┌──(kali㉿kali)-[~]
> └─$ curl -H 'Authorization: Basic YWRtaW46YWRtaW4='
> http://154.57.164.82:31397
> 
> <!DOCTYPE html>
> <html lang="en">
> ...
> ```

# GET Parameters
Once we are authenticated, we get access `City Search` function, in which we can enter a search term and get a list of matching cities

![[Pasted image 20260902080739.jpg]]

As the page returns our results, it may be contacting a remote resource to obtain the information, and then display them on the page. To verify this, we can open the browser devtools and go to the Network tab, or use the shortcut `CTRL+SHIFT+E` to get to the same tab.
![[Pasted image 20260902081424.jpg]]

When we click on the request, it gets sent to `search.php` with the GET parameter `search=le` used in the URL. This helps us understand that the search function requests another page for the results.

Now, we can send the same request directly to `search.php` to get the full search results, though it will probably return them in a specific format (e.g. JSON) without having the HTML layout shown in the above screenshot.

```
┌──(kali㉿kali)-[~]
└─$ curl http://admin:admin@154.57.164.82:31397/search.php?search=le

Leeds (UK)
Leicester (UK)
```

