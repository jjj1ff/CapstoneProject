# Specifications for streaming server

This is the root folder of the streaming server. The streaming server is for a helper and a person who needs help to communicate with webRTC.

## Description

A session is a video audio phone call based on webRTC between a blindman and a helper. A streaming server provides APIs for other servers to create and destroy sessions and APIs for applications to connect and stream.

A streaming server doesn't communicate with other streaming servers. A streaming server only provides sessions created on itself.

APIs for creating and destroying sessions can only be accessed by computers (usually servers) with a token. While creating a session, the accessor shall provide a session ID, a string which uniquely identifies the session and ensure there isn't two sessions has the same ID. The streaming server shall return one random string as the session Key for each of the blindman and the helper. Session Keys are strings and different devices has different Keys even though they has the same session ID which means they are assoated to the same session.

APIs for connecting to a session can only be accessed by devices (usually users' phones) who has a valid session ID and one of the session Key for the session. A session may be closed automately if either the helper or the blindman doesn't connected to the streaming server for a period of time. A session will explode after a longger period of time such as 48 hours.

A streaming server shall has a TLS certificate and all communications shall under TLS or DTLS either the certificate is self-signed or not. If the certificate is self-signed, the certificate shall be pre-shared.


## For Developers




## Settings

Settings are variables that setted in the "config.json" file and shouldn't be changed during run time. The "config.json" file should be correctly setted. Descriptions for each of the variables are below:

* `max_session_number`: Integer, the max number of sessions of the current server instance. A server shall reject creating sessions while the number of sessions is over the variable.

* `max_session_ttl`: Time period, expressed by strings like "*xx*h" or "*xx*m" or "*xx*s" for time in hours or minutes or seconds while *xx* is a integer number. If a session lasts for longer than the variable, the server may close the session.

* `max_bitrate`: Numbers of bits, expressed by strings like "*xx*K" or "*xx*M" for *xx* Kbps or *xx* Mbps.

* `tls_cert` & `tls_key`: String, the path of the tls cert and its private key in the form of PEM.

* `listen_address`: Array of strings. Each element for an IPv4 or IPv6 address. If there is only one valid IPv4 or IPv6 address and it is *0.0.0.0* or *::*, the server will listen to all addresses bind to it.

* `listen_port_http` & `listen_port_ws`: Integer, the port to listen. The two properties can be either the same or not.

## APIs and Messages

No HTTP Cookie used. Instead, parameters are usually stored in JSON strings or specially in request URL for HTTP GET method.

A JSON is specified by TypeScript object types for structure and natural language (Usually English) for meaning. A request URL is specified by JavaScript template literals in brackets () for structure and natural language (Usually English) for meaning.

The protocal or method of an API is specified in brackets []. For example, [HTTP GET -> WebSocket] means at the begining, it uses protocal HTTP and method GET and then it will shift to WebSocket.

Traffics are usually encrypted, so HTTP usually means HTTPS and WebSocket usually means WebSocket Secure.

### For clients (both helper and blindman):

* `connectSignalChannel` [HTTP GET -> WebSocket] (?session_id=\${session_id}&session_key=\${session_key}): Connect to the signal channel. \${session_id} and \${session_key} are the session ID and session Key associated to the current device. The WebSocket session shall be kept alive until one of the devices requires for hang up.

    #### Messages based on the signal channel:

    The possible directions of a message is specified in brackets {}. For example, {client -> server} means from a client to the streaming server; {server -> client} means from the streaming server to one client or both clients; {client -> server -> the other client} means this message shall be created and sent by a client; the server may take some actions base on the message or not and will relay the message to the other client in the same session.

    Messages shall be a JSON and follow the structure before:

    ```typescript
    {
        message_name: string,

        message_body: any
    }
    ```

    *message_name* shall be the name of a message specified in this doc or Related Docs at the last of the doc.

    *message_body* shall be the data the message contains which is usually a stringlified JavaScript object. If there is no *message_body*, the *message_body* property shall not exist. Then the JSON shall only have one property named *message_name*.

    * `sendSDPMessage` {client -> server -> the other client}: Send a SPD Offer or an Answer message to the other client. The server may check and reject to relay an invalid message. The *message_body* is the Offer or the Answer.
    
    * `getConnectionOrder` {client -> server}: Get if the current client is the first or second one to connect to the channel. (A simple way to provide an asymmetry) No *message_body*.

    * `hangUp` {client -> server -> the other client}: Hang up and destroy the session. After sending or relaying this message, the current client or server shall immediately close the WebSocket session and the WebRTC session if it is a client. Permission for accessing a TURN server may be withdrawed by the streaming server immediately. No *message_body*.

* `getSTUNServerList` [HTTP GET] ():
    Get a list of avaliable STUN server. The client can choose either one or multiple STUN servers in the list or do nothing.

    Responce body:

    ```typescript
    {
        urls: string[]
    }
    ```

    Each element in *urls* shall be a STUN URL which refers to a STUN server. For example: *stun:example.org*.

* `requireTURNService` [HTTP POST]: 

    Request body:

    ```typescript
    {
        session_id: string,
        session_key: string,
        current_stun: string
    }
    ```

    *session_id* and *session_key* are the session ID and session Key associated to the current device.

    Require for access to TURN service. The streaming server may response the STUN server if it is possible. (In this case, the STUN server is also a TURN server.) Otherwise, the streaming server may response other servers.

    Responce body:

    ```typescript
    {
        urls: string[],
        username: string,
        credential: string
    }
    ```
    
    Each element in *urls* shall be a TURN URL which refers to a TURN server. For example: *turn:example.org*.

    *username* and *credential* are parameters for TURN protocol to access the TURN server.

### For creating and destroying sessions:

//TODO To be done after auth method be determined.

* `createSession`: 

* `destroySession`: 

* `setSessionTTL`: 


## Related Docs