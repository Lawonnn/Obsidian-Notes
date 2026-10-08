# Request Methods

| Method    | Description                                                                                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`     | Requests a specific resource. Additional data can be passed to the server via query strings (`?param=value`)                                                            |
| `POST`    | Sends data to the server. It can me handle multiple types of input, such as text, PDFs, etc. It's common used when sending information (forms/logins) or uploading data |
| `HEAD`    | Request the header that would be returned if a `GET` request was made to the server                                                                                     |
| `PUT`     | Create new resources on the server                                                                                                                                      |
| `DELETE`  | Deletes an existing resource on the webserver                                                                                                                           |
| `OPTIONS` | Returns information about the serve, such as the method accepted by it                                                                                                  |
| `PATCH`   | Applies partial modifications to the resource at the specified location                                                                                                 |

# Status Code
| **Class** | **Description**                                                                                                                  |
| --------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `1xx`     | Provides information and does not affect the processing of the request.                                                          |
| `2xx`     | Returned when a request succeeds.                                                                                                |
| `3xx`     | Returned when the server redirects the client.                                                                                   |
| `4xx`     | Signifies improper requests `from the client`. For example, requesting a resource that doesn't exist or requesting a bad format. |
| `5xx`     | Returned when there is some problem `with the HTTP server` itself.                                                               |