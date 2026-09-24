Unlike HTTP `GET`, which places user parameters within the `url`, HTTP `POST` places user parameters within the HTTP Request body

# Login Forms
This exercise is similar to the one we already did in the `GET` section
![[Pasted image 20260902083346.jpg]]

If we try to login with `admin:admin`, we get in and see a similar search function
![[Pasted image 20260902083612.jpg]]

If we clear the Network tab in our browser devtools and try to log in again, we will see many requests being sent. We can filter the requests by our server IP, so it would only show requests going to the web application's web server (i.e. filter out external requests), and we will notice the following POST request being sent:
![[Pasted image 20260902084909.jpg]]

With the credentials `username=admin&password=admin` we can send a `POST` request. With the `-X` flag, we set the method we will use (default `GET`). Then, to add our `POST` data, we can use the `-d` flag and add the credentials

```
┌──(kali㉿kali)-[~]
└─$ curl -X POST -d 'username=admin&password=admin' http://154.57.164.82:32116

<!DOCTYPE html>
<html lang="en">
...
```

# Authenticated Cookies
If we were successfully authenticated, we should have received a cookie so our browsers can persist our authentication, and we don't need to login every time we visit the page.
```
┌──(kali㉿kali)-[~]
└─$ curl -i -X POST -d 'username=admin&password=admin' http://154.57.164.82:32116 -s | head

HTTP/1.1 200 OK
Date: Wed, 02 Sep 2026 12:22:28 GMT
Server: Apache/2.4.41 (Ubuntu)
Set-Cookie: PHPSESSID=5f1vml200q4he6q6mbcmgb4b3q; path=/ <--- HERE
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Vary: Accept-Encoding
Content-Length: 1554
Content-Type: text/html; charset=UTF-8
```

With our authenticated cookie, we should now be able to interact with the web application without needing to provide our credentials every time. To test this, we can set the above cookie with the `-b` flag in cURL, or with the `-H` flag:

```
┌──(kali㉿kali)-[~]
└─$ curl -b "PHPSESSID=nlstc9dhr4q2datngjr65qpp4o" http://154.57.164.82:32116
```
or
```
┌──(kali㉿kali)-[~]
└─$ curl -H "Cookie: PHPSESSID=nlstc9dhr4q2datngjr65qpp4o" http://154.57.164.82:32116
```

# JSON Data![[Pasted image 20260902093221.jpg]]
The POST data appear to be in JSON format, so our request must have specified the `Content-Type` header to be `application/json`

Indeed, we do have `Content-Type: application/json`. Let's try to replicate this request as we did earlier, but include both the cookie and content-type headers, and send our request to `search.php`

```
┌──(kali㉿kali)-[~]
└─$ curl -X POST -d '{"search":"br"}' -b 'PHPSESSID=nlstc9dhr4q2datngjr65qpp4o' -H "Content-Type: application/json" http://<ip>:<port>/search.php
```