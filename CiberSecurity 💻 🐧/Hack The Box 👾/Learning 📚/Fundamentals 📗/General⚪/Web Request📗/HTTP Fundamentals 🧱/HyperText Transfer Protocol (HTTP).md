# URL Structure
![[Pasted image 20260831085843.png]]

| Component    | Example                 | Description                                                                                                                                                         |
| ------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Scheme       | `http://`<br>`https://` | This is used to identify the protocol being accessed by the client, and ends with `://`                                                                             |
| User Info    | `admin:password@`       | This is an ==optional== component that contains the credentials used to authenticated to the host, and is separated from the host with `@`                          |
| Host         | `inlanefreight.com`     | The host, can be a hostname or an IP                                                                                                                                |
| Port         | `:80`                   | The port                                                                                                                                                            |
| Path         | `/dashboard.php`        | This points to the resource being accessed, which can be a file or a directory                                                                                      |
| Query String | `?login=true`           | The query string starts with a question mark (`?`) and consist of a parameter (like `login`) and a value (like `true`). Multiple parameters can be separated by `&` |
| Fragments    | `#status`               | Fragments are processed by the browsers on the client-side to locate sections within the primary resource                                                           |
Not all components are required to access a resource. The main mandatory fields are the scheme and the host, without which the request would have no resource to request.

# cURL
`cURL`(client URL) is a command-line tool and library that primarily supports HTTP along with many other protocols.
