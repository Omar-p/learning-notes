# Congestion Avoidance and Control:
  - additive increase, multiplicative decrease.


- TLS/SSL
  - create secure, protected tunnel across the Internet.
    - HTTP + TLS = HTTPS
    - SSL VPN
      - client can use ssl tunnel to connect to Coropote FireWall across the Internet and access its network. 
      - Connecting to public VPN Provider.
        - hide client IP from visited website and act as it coming from different src location.



- SSL/TLS cannot prevent the capture of data across the network, but it protect it in  ways:
  - [Confidentiality] data is only accessible by Client and Server. [Encryption]
  - [Integrity] detect any modification to the data. [Hashing]
  - [Authentication] validate identities of the client & server. [PKI: public key infra]
  
  TLS tunnel is a conceptual illustration.
    Data is still passing through other people's networks. but protected by tls as described above.
- SSL vs TLS
  - SSL
    - Secure Socket Layer - Created By Netscape in 1994.
  - TLS
    - Transport Layer Security
      - SSL maintenance handed to IETF in 1999
      - IETF renamed protocol to TLS.
  
- Anti-Replay and Non-Repudiation:
  - Anti-Replay:
    prevent man in the middle to repeat the same message[packets] sent from the client to the server. 
    so the server can detect those messages are repeated and reject them.
    - Provided with built-in sequence numbers.
    - Built in to [Integrity + Authentication].
  - Non-Repudiation:
    - sender cannot later deny sending a message.
    - Byproduct of [Integrity + Authentication]

----

TLS Key Players 
  - Client 
    - Entity initiating the TLS Handshake
    - Web Browser 
      - Phone, Apps, Smart Toaster, Iot
    - Optional authenticated (rare) 
      [mutual authentication ssl]         
      Mutual Transport Layer Security (mTLS) is a process that 
      establishes an encrypted TLS connection in which both parties 
      use X. 509 digital certificates to authenticate each other. 
      MTLS can help mitigate the risk of moving services to the cloud 
      and can help prevent malicious third parties from imitating genuine apps.
  - Server 
    - Entity recieving the TLS Handshake
    - Web Server
      - Apache, IIS, Nginix, etc..
      - Load Balancer or SSL Accelerator.
    - Always authenticated.
    
  - Certificate Authority
    - Governing Entity that issues Certificates
    - Trusted by Client & server.
    - Provides Trust Anchor
      - If we trust CA, we trust what the CA trusts
    - 5 Organizations secure 98% of the Internet:
      IdenTrust[Let's Encrypt] -> 51.9%
---
  ssl1.0 => 2.0 => 3.0 => TLS1.0 RFC 2246 => 1.1 => 1.2 => 1.3;
  
  in oct 2014 POODLE attack released, which allow decrypting ssl 3.0 traffic.
      DigCert [GeoTrust, Verisign, Thawte] -> 19.4%  
--
- TLS / SSL versions
  - SSL v1.0 1994
    - Developed by netscape
    - never publicly released 
    - full of flaws
  - SSL v2.0 1995
    - Developed by netscape
    - Complete redesign of SSL v1.0
    - Still full of flaws
  - SSL v3.0 1996
    - Developed by netscape
    - Complete redesign of SSL v2.0
    - foundation of TLS versions we use today [SSLv3.1 -- TLSv1.0, SSLv3.2 -- TLSv1.1..]
    - new features
      - introduce concept of certificate chains [a way to scale the world of ssl]
      - Added Compression capability [vulunabilities have found in it so no use this feature]
      - Optional Support for additional Key exchanges
        - SSL v1.0 and v2.0 only supported RSA Key Exchange
      - Preominant version of SSL through ~2014. 
        - Insecure as of ~oct 2014 due to POODLE attack
  
  - TLS 1.0 add minor change to SSL 3.0 so it become SSL 3.1.
    - HMAC Support 
    - Required support for additional key exchanges
    * Major vulnerability: BEAST (attack on CBC cipher)
      - Mitigated in most modern browser.
      
  - TLS 1.1 
    - similar to TLS 1.0
      - formally deprecated "EXPORT grade" ciphers.
      - protection against CBC attacks (i.g., BEAST)

  - TLS 1.0 and 1.1 deprecated MARCH 2021
  
  - TLS 1.2
    - Improve security of key generation.
    - introduced support for AEAD ciphers
      - Authenticated Encryption with Associated Data. 
      prior AEAD we do authentication in one step so if it a weak implementation of it. it will afffect the encryption. with AEAD we do both in one step so one weak don't compromise the other.
    - replaced MD5 and SHA-1 with SHA-256
      
  - TLS 1.3 [some changes] 2018:
    - shorter handshake (2 messages vs 5+)
    - 0-RTT(round trip time) Resumption.
      - u can resume prior tls session and send the first byte of encrypted data in the 
       same packet
    - Forward secrecy required. [cryptography]
    - AEAD ciphers required.
   [huge paradigm shift] Favors security and simplicity over backwards compatbility.
     - Insecure algorithms removed.
--
 SSL named TLS when netscape publish it and transfer ownership to I.E.T.F
 --
 Hashing
  - output of hash function called [digest, checksum, CRC, Hash..]
  - properties of practical hash func
    - Infeasible to produce a given digest.
    - Impossible to extract original message.
    - Slight changes produce drastic difference. [Avalanche Effect]
    - Resulting digest is fixed width. 
  - The Avalanche Effect refers to the fact that for a good cipher,
    changes in the plaintext affect the ciphertext. The algorithm 
    produces a completely different output for a minimally changed input. 
--
 Data Integrity:
   - client and server establish a secret key [in later lec, we will explain how]
   - client use key and message and hashing them send the message to the server.
   - the server combine the message with key and hashing them and compare it with 
     the recieved digest.
   - the key prevent man in the middle to intercept message and modify it and its digest
     because he don't have the key

   - and that provide integrity and authentication for bulk data transfer
     - Message Authentication code (MAC)
       - combining message + secret key when calculating Digest.
   - the client & server must agree with the key and how (message + secret key must be combined   in the same way).
   - Industry standard implementation if MAC: HMAC (how to combine message + key) -> digest
      - Hash based message authentication code RFC2104
--
Encryption:
  - Encryption is used to provide confidentiality.
  
  Simple Encryption
    - if u use simple encryption[plain text -> cipher text] and send the same message 
      to 2 different recipient. it will be the same cipher text. 
    - doesn't scale
    - hard to do securely   
    - cannot use a standard algorithm because everybody will know how to decrypt it.
    
  World use KEY BASED ENCRYPTION
  
  Type
    - Symmetric Encryption
      - Encrypt and decrypt using the same keys.
      - Faster - Lower CPU cost.
      - Cipher text is same size as plain text.
      - secret key must be shared - less secure.
      IDEAL for Bulk Data.
      - Algorithms: - AES - ChaCha20 [less: 3DES] [less: DES RC4]
      
  - ASymmetric Encryption
    - Encrypt and decrypt using different keys.
    - Slower - require much larger key sizes
    - Cipher text expansion
    - Private kay is never shared - More secure.
    - Restricted to Limited Data.
    - Algorithms:
      - [DSA weaker than others] - RSA[recommanded bit size 2048 bits] 
      - Diffie-Hellman - ECDSA - ECDH
-----
Public and private Keys:   
  - provide [confidentiality]
  - encrypted data with public key can only decrypted using the correlating private key.
  Asymmetric Key Pair [used for (SIGNATURE)]
  - [Authentication] [Integrity]
    - if one side[A] decrypt a message using his private key, if the other side[B] can decrypt it
      using the public Key of[A], now he can make sure this message is sent from A and no one
      modify it.
Hybrid Encryption[this how ssl/tls protect data]:
  - for bulk data, asymmetric encryption will be slower, so we generate a random
    symmetric key and share it with the other side using asymmetric encryption.
    - now both side have the symmetric key in a secure way, and can use it to share data
      securely with each other.
Signatures:
  - Entire message cannot be "encrypted" with Private Key.
    - Again, Asymmetric encryption has limitation
    - to overcome that, we hash the message and encrypte its digest[we got the signature]
    - append it to the message and send it. the other side[B] use the A public key to decrypt
      the signature and hash the recived message if they match, we achieve authentication and  integrity
    
  - Many different  items can be Signed
    , Messages .. but also Certificates, Software, EMail, etc..
-----
HOW SSL/TLS uses Cryptography
  - everyone can have a asymmetric key. so, how the client know the public key it use to 
    encrypt the generated symmetric key is belonging to the intended server. [how to authenticate the server]
    - here where the CA(certificate authority) coe to play. CA is trusted by Client.
    - CA will generate a Certificate that link an Asymmetric key pair to specifi identity.
    - certificate is Signed by the CA to detect any modifications.
    client trust CA --> CA trust the server --> [Anchor trust] client trust the server.
    (client + CA + server) form a triangle PKI [public key infrastructure]
    other PKI:
      code signing [os + code signing ca + software]
      internal corporate[employee + corporate internal CA + corporate resoruces[internal tools]]
      
---
- How SSL/TLS works
  - Assuming that a client needs to establish a connection with the server over SSL, TLS, the first thing it does is send a message to a server informing it about the versions of SSL/TLS that he understands and is willing to use.


  - Enclosed in this message is a randomly generated value that will be used to create a shared key.


  - The server in return responds with a message where it lists the SSL TLS protocol versions that it supports.

  - The server will also send its public key in the message that it sends to the client, plus a randomlygenerated string that will be used to create a symmetric key.



  - Now the client has the server. It will use it to encrypt a message to the server. This message is called the pre master secret, since this message was encrypted by the server's public key.Only the server's private key can be used to decrypt it.


  - The server uses its private key to decrypt the pre master secrets sent by the client.


  - Now, each of the server and the client has the following a randomly generated value that is unique to each one of them.


  - Both the client and a server used a randomly generated values that they exchanged to each other, plus the shared secret to create the symmetric key.

  - Now, this symmetric key can be used to encrypt and decrypt further messages sent between them.


  - The process of public key verification is called certificate signing and validation.