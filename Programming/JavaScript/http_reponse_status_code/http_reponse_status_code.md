## HTTP Response Status Code [Back](./../JavaScript.md)

### 1xx Infomational

#### 100 Continue

The Server has received the request headers and the client should proceed to send the request body (like a POST request). To have a server check the request's headers, a client must send `Expect: 100-continue` as a header in its initial request and receive a `100 Continue` status code in response before sending the body. The response `417 Expectation Failed` indicates the request should not be continued.

#### 101 Switching Protocols

The requester has asked the server to switch protocols and the server has agreed to do so.

#### 102 Processing (WebDAV)

A WebDAV request may contain many sub-requests involving **file operations**, requiring a long time to complete the request. This code indicates that the server has received and is processing the request, but no response is available yet.

### 2xx Success
