This is the root folder of the streaming server. The streaming server is for a helper and a person who needs help to communicate with webRTC.

Description
----

A session is a video audio phone call based on webRTC between a blindman and a helper. A streaming server provides APIs for other servers to create and destroy sessions and APIs for applications to connect and stream.

A streaming server doesn't communicate with other streaming servers. A streaming server only provides sessions created on itself.

APIs for creating and destroying sessions can only be accessed by computers (usually servers) with a token. While creating a session, the accessor shall provide a session ID and ensure there isn't two sessions has the same ID. The streaming server shall return one session keys for each the blindman and the helper.

APIs for connecting to a session can only be accessed by devices (usually users' phones) who has a valid session ID and one of the session key for the session. A session may be closed automately if either the helper or the blindman doesn't connected to the streaming server for a period of time. A session will explode after a longger period of time such as 48 hours.

A streaming server shall has a TLS certificate and all communications shall under TLS either the certificate is self-signed or not. If the certificate is self-signed, the certificate shall be pre-shared.


For Developers
----




Settings
----

Settings are variables that setted in the "config.json" file and shouldn't be changed during run time. The "config.json" file should be correctly setted. Descriptions for each of the variables are below:

* `max_session_number`: Integer, the max number of sessions of the current server instance. A server shall reject creating sessions while the number of sessions is over the variable.

* `max_session_ttl`: Time period, expressed by strings like "*xx*h" or "*xx*m" or "*xx*s" for time in hours or minutes or seconds while *xx* is a integer number. If a session lasts for longer than the variable, the server may close the session.

* `max_bitrate`: Numbers of bits, expressed by strings like "*xx*K" or "*xx*M" for *xx* Kbps or *xx* Mbps.

* `tls_cert` & `tls_key`: String, the path of the tls cert and its private key in the form of PEM.


APIs
----

