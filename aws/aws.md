# cloud computing
- On-Demand Self-Service:
  - ...can provision capabilities as needed without requiring human interaction.
- Broad Network Access
  - Capabilities are available over the network and accessed through standard mechanisms...[http, https,ssh,vpn..]. no need to visit datacenter.
  - if u need to go to datacenter to provision a new server, then it's not cloud computing.
- Resource Pooling
  - There is a sense of "location independence" ... no "control" or "knowledge: over the exact "location" of the resorces
  - Resources are "pooled" to serve multiple consumers using a "multi-tenant model"
  - Moved from thinking of hardware as value(instead of thinking about hw and servers, it think about it as the host) to thinking of applications and services as things of value that a business offers.
- Rapid Elasticity
  - capabilities can be elastically "provisioned" and "released" to scale "rapidly" outward(horizontal) and inward with demand.
  - To the consumer, the capabilites available for provisioning often "appear" to be "unlimited" 
  - Elasticity -> system automatically system size increase as the load increases, and automatically decrease when the demand decrease.
     - (vendor provide monitoring and auto-scaling)
- Measured Service
  - before cloud: u buy a server and u pay for it even if u don't use it. u pay in advance
  - Resource usage can be "monitored", "controlled", "reported" .. AND "billed".

  - through the course keep them in ur mind and for every svc evaluate how that service matches with one of this characteristics.
     - it help u in exam if the answer make sense or not, help u to design a system.
--------------------------------------------------------
# Public vs Private vs Hybrid vs Multi:
- Multi (cloud provider resilience)-> 
  - deploy ur app in multiple cloud provider this means that u have got cloud provider level resilience. 
    - stay away from single management window to manage multi-cloud because it add  abstraction that make only the common services between each platform are availables and can not use the rest. and that doesn't make use of each one.  
- Private -> 
  - private cloud on-premises [aws outposts, azure stack, google anthos] provide services dedicated to ur business and run on your-permises. 
  - they must meet the 5 characteristics of cloud computing. but dedicated to you as a business.
- Hybrid -> 
  - use public and private in the same time. with true hyber cloud. you use the same tooling the same interface to interact with both private and public component.
  - Hybrid Cloud is NOT public cloud + legacy on-permises -> it called hybrid environment or hybrid network.
-----------------------------------------------------------
Cloud Service model
- Infrastructure Stack -> collection of things which the application needs all stacked onto each other[Facilities -> infra -> servers -> virtualizations -> os -> container -> Runtime -> Data -> Application]. 
- Parts "You" manage, Parts managed by the "vendor".
Unit of consumption.[what you pay for, what you consume].

- on-permises vs data-center hosting
  data-center: provide racks, building and security. you manage the rest.
- IaaS: manage [Facilities, Infra, Servers, Virtualization] you consume the OS and mange the above layers. you pay per min ,secs, hours or virtual machine.
EC2 uses the IaaS service model.
- PAAS: u consume the Runtime and manage the above layers[Data, Application]
  if java app, u pay for a java runtime. PAAS mainly used by developers.
- SAAS: u consume the application. Netflix, Dropbox, Email.

# SECTION 2
- aws accounts and users inside of those accounts.
  - AWS Accounts is a container for identities (users) and Resources.
     - users -> log into that account and provision services in that account.
     - a unique root user for each account.
     - root user cannot be restricted. 
  - AWS ROOT USER' using Identity and Access Management(IAM) Users, groups, Roles can also be created and given full or limited permission.
    - u can create one AWS account for each stage of the development lifecycle or for each product/team.
      - to eliminte the damge that can be done by a bad actor or a mistake.
  - AWS Accounts can contain the impact of of admin errors, or exploits by bad actors, 
    Use separate accounts for separate things(DEV, TEST, PROD) or teams or products or client.

  - activate iam user access to billing information: 
    - to allow IAM Identity to access billing information.
------------------
# Multi-factor Authentication:(MFA)
  - Factor: different pieces of evidence which prove identity.
    -  Knowledge: Something you know, username & password.
    -  Possession: Something you have, bank card, MFA device/app. [ATM use possession and knowledge]
    -  Inherent: Something you are.. fingerprint, face, voice, iris.
    -  Location: A Location (physical), which network (corp or wifi)
    -  More Factors means more security & harder to fake.
  - we try to balance between convenience and security.
    - configuring MFA for aws , aws generate a secret key and other associated info (username & service name).
    - aws give u a QR code to scan with your MFA app (generated from the key & associated info).
    - the secret key is used to generate a 6 digit code which is valid for 30 seconds.
-------------------------------
- IDentity provider
  - Authenticate Identity
  - Authorize based on policies associated with the identity I authenticated to.
  - NO COST 
  - there are limits
- Global service / global resilience
- IAM of your account is trusted fully by your account, can do anything.
    - if IAM allow one identity to do something, the account automatically trust the identity in the same way it trust IAM.
    - No direct control on external accounts or users. control only local identities in your account.
    - make use of identity federation -->[take identities u have[fb account] and use it indirectly to use aws] and MFA

  - every aws account come with its own running copy of IAM, it's own db
  - IAM is globally resilience service, any data is always secure in any 
    aws regions.
    - IAM u see in each of ur AWS accounts is  your own dedicated instance of IAM separate from other account and from any one else accounts. 
     - my AWS account trust my  instance of IAM.
     - IAM is trusted fully by the account, IAM as a service can do as much as the root user.
      - inside IAM we can create other identities, IAM can allow those identities to <br> do certain things. because a single aws account trust IAM, if IAM allows one of identity that it manages to do something, the account automatically trust the identity in the same way it trust IAM.
       
       
- IAM ---> 
  - User --> Identities which represent humans or applications that need access to your account.
  - Group --> Collections of related users e.g. development team, finance or HR
  - Role ---> Can be used by aws services, or for granting external access to your account. [when you want to grant access to services in your account to an uncertain number of entities]. 
      - Example for IAM ROLE -> if you want all EC2 instances in your account to be able to access the simple storage service, then you can create a role which grants access to the simple storage service, and then allow instances to use that role.

 - In Brief: 
    - you pick IAM users when you can identify the individual thing that will log in to that user.
    - Roles tend to get used when the number of things is uncertain.
  
  - IAM policy(iam let u create it) [object or document]: 
    - used to allow or deny access to aws services when and only when they're attached to IAM USERS, GROUPS OR ROLES.
    - policy on their own do nothing they simply define, allow or deny rights to a certain  services when you attach<br> the policies to other identities. so users, groups and roles do they have any effect.
  
  - IAM has 3 main jobs:
    - 1- Manage Identities- And ID Provider(IDP)
      - allow to create, modify, and delete identity such as users and roles.
    - 2- Authenticate those identities
      - anyone attempt to make a request to aws(a principal) need to be authenticate, attached to
        a known identity.
    - 3- Authorize: allow or deny access to resource based on policies associated 
      with the identity that I authenticate with
  
### IAM 
  - NO COST
  - Global service/Global resilience
  - No direct control external accounts or users. only control local identities in your account.
  - allow or deny its identities on it AWS account
  - Identity federation and MFA
    - take identities that u're already have, and use this indirectly to access aws 
      resources
    
<hr>

### IAM Access Keys:
- Long-Term Credentials ...[don't change regularly or automatically](for IAM user only)
- IAM USER
  - has 1 username and 1 password[u can create Iam user to used only in cli or api access and those don't need a password] password on IAM user is optional.
  - two access keys [can be created, deleted, made inactive or made active]
      - Access key id + Secert Access Key --> generated by aws and show to only only once
        - so you need to store them securly. used to sign your request that sent to aws.
      - can not be change. but you can delete it and create new one.
- root user can has access ky but it's not recommended. 
  - iam user can has access key, but iam role don't.
- Rotating Access Key. create new one and remove the old one that is why they allow two.
  - IAM USERS and ROOT user can use access key. it's not recommended to the root user.
- IAM ROLE don't use access keys.   

- in cli to run command from specific profile
  - aws [cmd] param (flages)  --profile name
- to configure a new profile:
  - aws configure --profile name
    - enter aws access key id & secret.

- u can create it under My security credential tab in IAM console.
- any one with that access key id and secret, they can utilize the iam identity u've configure <br>
together with any permissions that identity has over this aws account.
    
-------------------SECTION 4-------------------------------

### Yaml 
- Yaml ain't markup lang [identation matters]
    - human readable data serialization language
      - list: [] or  <- inline format.
      - list:
      	  - item1
      	  - item2
    - supporting val types:
      - numbers, floating point, boolean, null.    
    - vals can be enclosed in "", ``, or not.  but enclosing are more precise.

    - we can have value formed by multiple attrs[key/vals pairs]:
      - adrianscats:
        - name: nn
        - color: white

    	
- generally, YAML is only used for CloudFormation.
   - Json is used for both CloudFormation and other things(identity policy doc..)
<hr>

### JSON
  - vals types: [string, object, number, array, boolean, null]

<hr>

# Encryption
  - Encryption At Rest:
    - to protect aganist physical theft and physical tampering. [user with encrepted laptop]
    - common when only one party is involved. one person ;one encryption and decreption key
    - common when one entity involoved.
  - Encryption In Transit:
      - protecting data while it's being transferred between two places.
      - used when multiple system is involved.
  
  - Concepts:
    - plaintext: data[image, file, app] u can immediately load into an application and read that data.
    - Algorithms: Blowfish, aes, rc4, RSA..
    - Key: password to encrypt 
    - Ciphertext: encrypted data.
  
  - Symmetric Encryption: the same key used in both encryption and decryption process.
      - limitation -> how we will transfer the key between two peer [getting complex very quickly]?
        - because of that limitation, symmetric encryption is great in  local file encryption or disk encryption. but not so useful when data is moved between 2 parties. 
  - Asymmetric Encryption:
      - u have public and private key, when someone need to send me a data, he will encrypt it with  my pub key and send it. then I use the private key to decrypt it. used by[SSL, TLS, SSH]
    
    - Asymmetric encryption is computationally much more diffcult to do than symmetric. so, many processes use asymmentric to initially agree and exchange a symmetric key and then use it in the communication, use symmetric encryption.
    
    - Another way for using asymmetric keys is [signing]: for ID verification: u sign using your Private key and send the request the receiver can verify that is you by using the public key to validate the signed request.
    
  <hr>

- Steganography: hiding that you are encrypt a data, by hiding the encrypted data in another file. 
      - using a certain key, a number or a pattern[hide data in an image, by changing some pixels' val].

<hr>

# Digital Signatures:
  - verifies integrity (what) & Authenticity(who)
  - HASH of the data is token, original data remains unltered (integrity)
  - this allows normal use without worrying about hashing/keys. 
  - Digitally sign the hash with a private key (Authenticity)
  - public key can be widely distributed and trusted.
    - if u distribute it in many places, it's easy to spot if one of them is modified.
  - .. hash cannot be changed as nobody has the private key.
  - document cannot be changed as this invalidates the hash.
  - Trust public key => trust private key => trust entiry => trust data.
  - u can take it step further and encrypt the hash and data with the public
       key of the receiver. so, only the receiver can decrypt it. 
------------------------------------------------------------------------------

# Networking:

## Layer 1 - Physical
  - specifications define the transmission and reception of RAW BIT STREAMS between a device and  a SHARED PHYSICAL MEDIUM. 
   it defines things like VOLTAGES LEVELS, TIMING, RATES, DISTANCES, MODULATION, 
    and CONNECTORS. Copper(electrical), Fiber(light) or WIFI(Radio Frequency)
  - to connect two devices you need only a link(point to point) between them.
  - but when we need to connect multiple one. we need a hub[anything recieved on any port, is
    transmitted on every other port] including errors & collisions.
  - At the stage of layer one networking:
     - No INDIVIDUAK ADDRESSES, At layer 1, one laptop can not address traffic directly at another.
      it's a broadcast medium.
     If multiple devices transmit at once. a collision occurs.
     L1 has No media access control and no collision detection.[which devices can transmit]
     cannot detect collision it just transmiting in voltage change.
     No device => device communication. it just broadcast to all connected devices.

## Layer 2 - Data Link Layer
  - every thing above this layer rely on device to device communication which the data link  layer provides.
  - Devices at L2 have a unique hardware (MAC 2 part OUI of the company + network interface controller = unique MAC) address. <br> 48 bits, in hex, 24 bits for manufacturer.
    - Frames can  be addressed to a dest or broadcast(ALL F's)
  	- FRAME
      - ![l2-frame](images/osi/layer2-frame.png)
  	
  	- To avoid collision L2 checks for any sign of a carrier signal
      - ![l2-csma-cd](images/osi/layer-csma-cd.png)
  	  - jam signal is sent by all of the devices, and random backoff occurs(a period of time during which no device will attempt a transmission.) after that period which picked randomly a transimission is retried.
  	  
    - L1 & L2 
  	   - to connect more than 2 devices and use benefit of L2. a hub won't work because it doesn't understand L2. So we use a SWITCH.
	    
    - SWITCH:
      - understand frames and MAC. they maintain a MAC ADDRESS TABLE which starts off EMPTY. 
      - As the switch recieves frames on its ports, it learns which devices are connected and populates the Mac address table.
      - <b>STORE and FORWARD</b> ..they don't repeat blindly like hubs. it means that ONLY VALID FRAMES are FORWARDED 
        - collisions is isolated on the port they occurred. 
	      - Every X port switch has X collision domains. It allows switches to scale and be connected together.
	    
  - L2
    - identifiable devices
    - Mac
    - Collision Detection
    - Unicast 1:1
    - Broadcast 1:All
    - 
  
### L3 Network
  - LAN1 and LAN2 are isolated LOCAL AREA NETWORKS. 
    - Using only layer 2, only those networks joined by a direct point to point linnk using the SAME layer 2 protocol could communication.
  - Ethernet is a L2 used generally for local networks. 
    - Long distance point to point links will use other more suitable protocols such as ...PPP/MPLS/ATM[satellite..]
  - IP is a L3 protocol which adds cross-network IP addressing and routing to move data between LAN without direct P2P links.
  - Routers(L3) devices remove frame encapsulation and add new frame encapsulation at every hop.
  
  - protocol field in IPv4: 1 -> TCP , ICMP -> 6, UDP -> 17.
  - TTL in IPv4 -> Hop Limit in IPv6
  
  - IP divided into network part and host part.
  - IP Addresses are assigned by machine(DHCP) or Humans.
  
  - Subnet Mask
    - It's the Subnet Mask which allows a HOST to determine if an IP address it needs to communicate with is local or remote 
      - which influences if it needs to use a GATEWAY or can communicate locally.
    
  - L3 
    - Route Tables & ROuters
      - Router compares packet dest Ip ^ route table for matching destinations.
        - The more specific prefixes are preferred (0 lowest, 32 heights).
        - Packet is forwarded on to Next Hop/Target.
        
  	  - Route Tables can be statically populated, or there are protocols such as BGP(Border Gateway Protocol),<br> which allow routers to communicate with each other,to exchange which networks they know about.
	  
	- ARP used in L3 when u encapsulate the packet and then send it to a MAC address, u don't know now the MAC address. <br> so we need a protocol which can find the MAC address for a given IP address. 

L4 Transport Layer
  L3 problems:
		L3 provides no ordering mechansim.
		L3 is not guaranteed to be reliable.
		L3 diff packets can experience diff delays.
  	L3 has no communication channels - packets have a Source and Destination IP but 
  	  no mthod of splitting by APP or CHANNEL.
	  No flow control if src is faster than dest. dest can be overwhelmed.
  
  TCP segments:
    segments don'y have SRC or DEST IP's
    Options - Padding
    SRC PORT  -		DEST PORT : provides the ability of having multiple streams of data at the same time between 2 devices
    SEQUENCE NUMBER: increment after sending each segment. unique, can be used for error correction. used in reordering.
    ACKNOWLEDGEMENT: 
    FLAGS 'N' things: used to control over the connection[[flags]FIN: finish the connection, 
     ACK: acknowledgement, SYN: sync seq numbers]. 1-close the connection.    2-synchronize sequence numbers. 
    	data-offset. reserved space.
  	WINDOW: #of bytes u are willing to recieve between acknowledgements. [flow-control use it]
  	CHECKSUM: detect error
  	URGENT POINTER: to detect segments of controlling the traffic to take priority of processing. separate between 
  	app segments and traffic segments.
	
	TCP is a connection based protocol. A connection is established between 2 devices using random port on 
	client[EPHEMERAL PORT] and a [WELL-KNOWN PORT] on the server. Once esablished the connection is bi-directional.
	 reliable, provided via the segments encapsulated in IP packet.
	
	-way handshake:
	  to esablish the connection between client and server. they need to agree on the sequence number the client choose one randomly and then increment it for 
	   the next packets. the server also choose one to start with it for the packet will be sent to the client.
	
	SESSSION & STATE:
	  A Stateless firewall would see two things.
			Outbound .. client to server
			Response ..
			TWO RULES will be required... OUT & IN
			Networ ACL(AWS): access control list   - need 2 rules for each TCP connection.
	
	 A Stateful firewall views sees one thing [aware of tcp protocol]
	   Outbound .. Allowing the outbound implicitly allows the inbound response.
	   In AWS this is how a security groups works.
	   stateful firewall understand layer four and the state the traffic.
--------------------------------
Elastic Beanstalk [ ur code, app service, http service, os, interpreter, host]
  - Paas - allows quick deployment of ur app.
  Keeps control in ur hands:
    - choose ur instance type - choose ur database - set and adjust auto scaling
    - update ur app - access server log files - Enable Https on load balancer.
    * support a large range of platform.
----------------------
  ..TLS ...asymmetric and then symmetric.
  TLS start when that a TCP connection is active between the client & server..
   so this is layer 4 and ath the end of the 3 phases.
   
   1 - Cipher suites : is a set of protocols used by tls[a key exchange algorithm, a bulk encryption algorithm, message authentication code algorithm]
   *begin with established Tcp connection. client send ssl/tls version, list of supported cipher suites, session id, extensions. 
   * server respond with its ssl/tls version, a selected cipher suite from the client list, server certificate[contain a 'public key'].
   
   2 - Authentication: the client trust the public CA - it makes sure that the certificate 
       is valid (date, annd hasn't revoked) and that the DNS name matches the name/names on the cert.
     - the client verfies the server has the private key.
     
   3 - Key Exchange:  - the client generate the pre-master key, encrypt it
                        with the servers public key and sends it to the server.
                      
                      - the server decrypt the pre-master key using its private key.
                      
                      - both sides use the same pre-master-key to generate the master secret  
                        which is used to generate the ongoing session keys which encrypt and 
                        decrypr data.
                      - Both sides confirm the handshake and from them on,
                        communications between client <=> server are encrypted   
----------
DDoS Attacks
  - Attacks designed to overload websites
  - compete against 'legitimate connections' and overload hardware & software provide the svc
  - Distributed - hard to block individual IPs/Ranges
    - Application Layer - HTTP Flood
      - take advantage of the imbalance of processing between client & server.
         it's easy for the client to request a web page but it's often very complex for a
          server to deliver the same page
    - Protocol Attack - SYN Flood
      - A Botnets generate a huge #of spoofed SYN's (connection initiations) the server sees
        these as normal and sends SYN-ACK's back to the spoofed IPs.
      - the servers will wait for an ACK ..which will never happens as the remote IPs will never 
        respond.   servers will consume available network resources attempting to establish 
        connections and won't be able to service legitimate connections.
    - Volumetric - DNS Amplification  
       - An attackers controls network of compromised devices(botnet) via a control
         location(often using vpn to digest real location)  
       - A botnet exploits a protocol where a response is significantly larger than the
         request. in this case making a spoofed request to DNS.
         
         .The DNS servers respond to the `spoofed IP`, the frontend servers for our application,
          which is overwhelmed by the amount of data. this prevents legimate customers accessing 
          services. 
    - ..often involve large armies of compromised machines(botnets) 
--      ---
Network Address Translation(NAT):
  - designed to address the growing shortage of IPv4 addresses.
      - IPv4 addresses:
        - publicly routable: assigned by central agency & regional agencies, which in turn 
          assign them to ISPs, ISP allocate them to business or consumer end-users          
        - private address space:
            in range 10.0.0.0 can be used in different places, but can't be routed over the internet.
            - to give internet access to private devices, we need to use NAT.
  - provide some securitiy benefits          
  - Translate private IPv4 addresses to public
  - Static Nat - 1 private to 1 (fixed) pubic address (IGW) [in NAT table 1:1 private:public]
    - translate from 1 specific private ip address to 1 specific public address[this how IGW within aws works]
    - used when 1 public ip need to be consistent.
  - Dynamic NAT - 1 private to 1st available public[from address pool]
    - same as static but a public ip is not permnent for a device, it's allocated temporarly from a pool.
    - if a device from a private network attempt to access the internet and no available public ip,
      this access will fail
    - when we have large #of private ip address and want them all to have internet access via public IPs.
      but when we have less public ip addresses than private ip addresses.
  - Port Address Translation(PAT) - many private to 1 public (NATGW)    
    - allow a large #of private devices to share one public address, it's how the AWS gateway functions\
      within AWS environment, it has a many to one mapping architecture.
    - THe NAT Device record the src(private) and src Port. it replaces the src IP with the
      single public IP and a public source port allocated from a pool which allows IP overloading(many to one)
      PAT record the mapping in the NAT table, to translate the incoming packet from the internet to its
      proper ip & port.
  - IPv4 only ..make no sense with IPv6
---

- IPV4
- originally managed by IANA
  -.. parts now delegated to regional autorities
- All public IPv4 addressing is allocated
- Part of the address space is private, can be used/reused freely.

Class A: - 000.000.000.000 -> 127.255.255.255 128 Networks, 16_777_216 IPs each
Class B:

- 128.000.000.000 -> 191.255.255.255 16_384 , 65_536 first 2 octat for network
  Class C:
- 192.000.000.000 -> 223.225.225.255 2_097_152 , 256 first 3 octats for network
  Class D: multicast

---

Private IP
[10].0.0.0 - -> [10].255.255.255 01x class A
[127.16].0.0 - > [172.31].255.255 16x class B 16*65536 172.31 is used for default VPC
[19.168.0].0 - > [192.168.255].255 256x class C 256*256 home & office network

---

subnetting - CIDR Classless Inter-Domain Routing - let us take networks and break them down - it define a wat of expressing the size of a network -> prefix[newtork bits]

---

DNS

- a discovery service.
- a huage distributed DB.
- DNS Client = > ur laptop, phone, tablet, pc.
- Resolver: swf on ur device or a server which queries DNS on ur behalf.
- DNS zone => A part of DNS DB, Point at TLD authoritative servers where is
  {.com} {.uk} [what data is]
  - gTLD => generic top level domain(.com .org
  - ccTLD => country-code top level domain(.uk .eg)
  - ZONEFILE => contain the records for a specific zone{ex: amazon.com} hosted by a
    nameserver. {physical database for a zone}. how zone physically stored.
  - NAMESERVER => where zonefiles are hosted, host zonefile.
  - we need to find the ns which host the zonefile we query for
  ***
  - last invisible dot in a domain name is the root zone
- DNS Root Server (hosts the DNS root Zone)
  - DND Root Servers(13) managed by 13 different companies.
    - they manage the root servers themselves not root zone database.
      - {Root hints files} stored inside the resolver point to the list of Root servers IPs.
      - Resolver communicate with 1 or more root servers to access Root Zone.
  - Root zone managed by IANA which delegate each root to another organization to manage.
  - first, u only trust the root servers, then u trust the root servers to tell u who
    manage the TLDs, then u trust the TLDs to tell u who manage the domains.
      - delegation of trust.
  ***
  Client 
  <br>--> resolver -using root hint files
  <br>--> Dns root servers root servers send the address of the server manage the top level root u search for (.com)
  <br>--> then u ask for (amazon)
  <br>--> resolver query amazon.com for www and send the result to the client
  <br>--> client go to www.amazon.com
  - <img src="images/dns-resolution.png" alt="dns-resolution" width="800" height="500"/>
  - Root hints => provided by os vendor, stored in resolver, list of root servers IPs.
  - Root servers => managed by 13 different companies, host the dns root zone.
  - Root zone => managed by IANA, delegate TLDs {authoritative servers}.
  - TLDs => managed by different companies, host the TLDs zone. 
  

---

Route53 Fundamentals:

- AWS manged DNS product.
- Global Service .. single database, globally resilient

  - Register Domains
  - Host Zones.. managed nameserver
  - services provided:
    - Register Domains: - it has relationships with all the major domain registries[.com .net .io .org] - when a domain is registered a few things happen: - R53 checks with the registry for that top level domain if the domain is available - R53 create a Zonefile for the domain being registered and allocates name service
      for this zone, and these are servers which R53 create and manages which are
      distributed globally and are generally 4 of these for one individual zone[hosted zone] - as part of registering the domain it communicates with the .org|com registry  
       and liaisng with that registry, it add these name server records into zone file
      for .org|com top level domain. using name server records which they after that
      delegate to host zone[authoritative for the domain] in the future
  - Hosted Zone[4 servers for one individual]
    - create and manage Zone files in AWS
    - Hosted on four managed name servers
    - can be public
    - Or private .. linked to VPC(s)
    - ..Store records (recordsets)

---
# Records Type

- Nameserver (NS) => allow delegation to occur in DNS, point to the name server
  authoritative for a domain. {the next node in the tree}
- A and AAA records =>   map host to IP address, A map to ipv4, AAA map to ipv6.
  - generally, as an admin u normally create 2 records with the same name, one for ipv4
    and one for ipv6.
- CNAME => canonical name, map one name to another name.

  - ex: www.amazon.com => www.amazon.co.uk 
  - CNAME ftp, CNAME mail, CNAME www all point to the same server.
   - <img src="images/dns-cname.png" width="800"height="500">
   - cannot point to ip address, only to another name.
- MX 
  - mx-record is for how server can find mail server for a specific domain.
  - when sending email to hi@google.com
    - send mx query to google.com.
=> mx record has a priority{smaller is highest} and value
  - value : 
    - it can be just a host, if it just a host no dot on right, it assume to be part of the same zone.
      - ex: if value in google.com zone is mail, then it is mail.google.com
    - if it contain a dot on the right, it is a fully qualified domain name.
      - ex: if value in google.com zone is mail.other.domain.

    - <img src="./images/dns-mx-record.png" alt="mx-record" width="800" height="500">  
- TXT Records
  - allow u to add arbitrary text to a domain .
  - one usage is to prove domain ownership.
    - <img src="./images/dns-txt-record-ownership.png" alt="txt-record" width="800" height="500">
  - it can be used for spam protection.
    - add certain information to domain indicating which entities are authorized to send email on ur behalf and if any email server receiving mail from any other servers that is a good indication this is a spam.
  

- TTL . u can set on DNS record.
  - if u may change ur dns record u should lower TTL value to low value before changing the record,
   to make sure that the change is propagated quickly. no machine will cache the old record for long time.
   - <img src="./images/dns-ttl.png" alt="dns-ttl" width="800" height="500">

-------
public Vs Private Service

- <img src="./images/private-public-services.png" alt="private-public-service" width="800" height="500">
- public & private services term are referring to networking only.
  A public service is something is accessed using public endpoints such as the Simple Storage
  Service(S3), S3 can be accessed from anywhere which has an internet connection.
- private aws service is something which runs within VPC. only things that are connected to
  that VPC can access the service.
- for both of these, there are a permissions as well as networking. even S3 is public
  service. by default an identity other than the account root user has no authorization
  to access that resource.

  - Permissions & Networking are two different considerations when talking about
    access to a service.

- "Public Internet Zone" where Internet services lay[gmail, online game..]  
  "AWS PRIVATE zone" are called Vitual Private Clouds or VPCs, there are isolated.
  VPCs can't communicate with each other unless u allow it. and nothing from the internet
  can reach these private networks unless u configure it. service can be placed into
  these private zones such as EC2 instances and just like the home private network it
  can access the internet and the internet can access it iff u allow it and configure it .
- "AWS Public Zone" between public internet and "AWS private Zone", it's not on the public
  internet and it's not part of public internet, it's a network which is connected to the
  public internet.
  It's where aws public services operate from, Services with public endpoints(S3).
  - u can access AWS public services from anywhere with an internet connection because
    the internet is used to carry package from u to the aws public zone and back again.
  - we can also configure virtual or physical connections between on-premises network and AWS
    VPCs.
  - u can also create and attach Internet gateway to a VPC. this provide a few additional
    pieces of functionality:
    - allow private zone resources to access the public internet as long as EC2
      has an allocated IP address0
    - allow access to public AWS services, without touching the public interne

---

AWS Global Infrastructure:

- AWS region

  - creation of AWS, an area of the world that they have selected,
    and inside this region is a full deployment of AWS infrastructure.

  - Geographic Separation - Isolated Fault Domain.

  - Geopolitical Separation - Different governance. u'll affects by the laws
    of the country where the region is located.
  - Location control - performance.

  .Region has CODE & NAME: console UI using name, CLI use code

- AWS edge location
  - smaller than regions, and only have content distribution services,
    as well as some type of edge computing, but they are located in many
    more places than regions.
  - useful for company such as Netflix, who need to store TV shows and movies
    as clos to their customers as possible. because this allow low latency and
    high speed distribution.
- AWS Availabilty Zone : is a logical thing inside AWS
  - some think about it as it's a data center, but it's not. it may formed by multiple data centers, or it may be a single data center.
  - each AZ is isolated from other AZs, and they are connected by high speed
    low latency network.

  - inside every region AWS provide multiple availability zones,
    isolated infrastructure inside a region.
    - u can distribute ur compnents across multible zones.
    - services can be placed across multiple availability zones
      to make them resilient[VPC a way to create a private network
      can work across multiple availabilty zones to provide resilience]

- globally resilient service
  - there are relatively few of those inside AWS, the service operate globally with
    a single database, it's one product, and its data is replicated. across multiple
    regions inside AWS. this mean a region can fail and the service continues running.
    - it would take the world to fail for a globally resilient service
      to experience a full outage.
      - the service is global means there isn't a concept of picking a region
        for globally resilience service [IAM , ROUTE 53]
- regionally resilient service
  - operate in a single region, with one set of data per region
- zone resilient service
  - AZ, services run from a single availabilty zone if if the AZ that service is
    provisioned into fails, then that service will fail.
    - it's possible for hardware to fail in an availability zone and for AZ
      itself continue working because AWS do have resilient storage systems
      for example inside AZ. But generally, AZ resilience services are very prone
      to failure if u have any problem inside that AZ
      into fails.
- at a global level aws have created their infrastructure platform to
  be a collection of individual infrastructure located worldwide.

---

VPC virtual private cloud= A virtual network inside AWS {regional service}:

- VPC is a service u 'll use to create private networks inside AWS, that other
  private services will run from.
- also used to connect ur AWS private networks to ur on-premises networks when creating  a hybrid environment.
- or it's the service which lets u connect to other cloud platforms, when u're creating a multi-cloud deployment
- A VPC is within 1 account & 1 region.
- private and Isolated unless you decide otherwise, SVCs in a VPC can communicate but svcs in different vpc cannot unless u decide otherwise.
- TWO types - Default VPC[1 per region] and Custom VPCs[many per region]
  custom vpcs need u to configure everything e2e in details, 100% private

  - used in almost all serious AWS deployments.
    default vpc created by AWS, they can pre-configured in a very specific way
    and all of the network configuration is handled on ur behalf by AWS. less flexiable.

- Every VPC is allocated a range of IP addresses called the VPC CIDR.
  - every thing inside the VPC will have an IP address from this range.
  define the start and end range of IP addresses that a vpc can use.
- Custom VPCs can have multiple CIDR ranges, but the default VPC can gets one.
  and it's always the same: 172.31.0.0/16
- VPC is regionally resilient, Aws sub-divide the VPC network into subnets,
  one for each availability zone. set on creation and never be changed.

---

Default VPC facts

- One per region - can be removed & recreated.
  some SVCs inside AWS assume that the default VPC will be present.
- Default VPC CIDR is always 172.31.0.0/16
- /20 Subnet in each AZ in the region
- pre-configured (IGW), security groups(SG),
  NACL: security feature to limit incoming & outgoing data transfer.
- Subnets assign public IPv4
- it's best practice to use custom VPCs not to use default VPC.
---

Elastic Compute Cloud (EC2)[IAAS]:

- provide access to a virtual machine (instance)
- Private service by-default - uses VPC networking. EC2 instance is configured
  to launch into a single VPC subnet. u set it when u launch the instance.
  in the launch u configure any public access should u want that because it is by
  default a private service. the VPC u run in need to support that public access
- AZ resilient
- Different instance sizes and capabilities[GPU, networking, special cpu]
- On-Demand Biling - Per second.
  - charge for running the instance, cpu memory
  - charge for storage and then extras for any commercial software.
- Storage type: Local ob-host storage or ELastic BLock Store(EBS) ->
  network storage made available for the instance.
- Instance Lifecycle [States]:  
   Running - Stopped[cost storage] - Terminated - Stopping(u'll charge for ebs storage) - Pendding - shutting down
  Terminating an instance[terminated] : is a non-reversible action.
- AMI Amzon machine Image \*\*  
  --Permissions: control which accounts can/cannot use the ami
  - public - everyone allowed [linux/win]
  - Owner Implicit allow
  - Explicit - specific AWS accounts allowed
    -- Root Volume: can contain other volume but at least one(boot volume)
    -- Block Device Mapping:
    configuration which links the volumes that the AMI has and how they're presented
    to the OS. it determines which volume is the boot volume and which is data volume.
  - the OS expects to receive volumes presented to it as well as an ID, a device ID
    And the block mapping links the volume to the device ID that the OS expects.
    - Connecting to EC2 : 
      - connect to win instances using RDP remote desktop protocol. 3389 port. - to linux instance using ssh port 22.
  - ssh key pairs: u download the private once and keep it safe.
  - public part aws place it on the instanc. u use the private part for authentication.
  - for win instances using private key to gaining access to the local admin pass  of the instance and u connect to it using rdp using local admin user and its password.
- ssh client will prevent u from connecting to an instance if the key is too open.
  - u need to change the permissions on the key file to 400.

---

S3:

- Global [public service] Storage Platform - regional based/resilient.
  because bucket name is unique, u don't need to select a region when
  using a console. u specify the region in creation.
- Public service, unlimited data & multi-user.
- ...Movies, Audio, Photos, Text, Large Data sets.
- Economical & accessed via UI/CLI/API/HTTP.
- Objects & Buckets
  - Objects:
    - u can think of it as a file, it made up of two main compnents
      and some associated meta-data. objectKey: objectVal.
      size 0 to 5 tb.
      if u know the object key and buket u can access data.
      object also has: Version ID, Metadata, Access Control, Subresources.
  - Bucket:
    - created in specific region. never leave unless u configure it.
    - bucket name is globally unique.
    - flat storage. all data on the same level. all in the root. folder is prefix for the name not a real folder.
    - 3 - 63 chars, all lower case, no underscore
    - start with num or lower case
    - can't be ip formatted.
    - Buckets - 100 soft limits, 1000{can get it by support request} hard limits per acount.
    - unlimited object in bucket, 0 bytes to 5 tb.
    - Key = name, Value = data.
- Anti-pattern & pattern.
  - S3 is an object store - not file or block. {prefered for images.. data accessed as a whole}
    if u want to access the whole object[image, video..] then it's candidate for object storage.
    if u have a server need to access a network file system. it's not S3. u need file-based stor
  - u can't mount an S3 bucket as (K:\ or /images
  - great for large scale data storage, distribution or upload.
  - great for 'offload' blog with many posts and images.
  - should be ur defaut for Input / Output from /to AWS products
  - if u will use it for static hosting, u need to name the bucket as the domain name.

---

CloudFormation:

- let u create, delete, and update aws resources in a cnosistent and predictable way using templates.
- written in yaml or json.
- resource section of a template is the only mandatory part of a cloudFormation template.
  - conain logical resources labelled as Instance: need a Type
  - properties to configure the instance in a certain way
- if u have AWSTemplateFormatVersion and Description then the description
  need to follow the template format version.
  - AWSTemplateFormatVersion is not mandatory but if u have it and Description
    then the description need to follow the template format version. {trick Q in aws exams}
- Metadata section control the UI
  
- Parameters section
  - u can add field prompt users for information.
  - u can restrict allowed value
- Mappings section
  - allow to create lookup table
  - <img src="images/mapping-cfn.png" width="800" height="500">
- Conditions section
  - based on some condition do->
- outputs:
  -set of outputs.  
-cloudFormation take a template create what specified in resource in a [Stack]  
-stack is a living representation of the template.
-one template can create 1 to many stacks
-for any logical instance in the stack aws create a physical one match it.
-it's cfn job to keep logical and physical in sync.
  - <img src="images/logical-physical-cfn.png" width="800" height="500">

---

CloudWatch{}:

- Collect and manages operational data generated by an env to inform how it perform
  how it nominally runs, or any logging data that it generate.
   - Metrics .Aws Products, Apps, on-premises. [cpu utilization of ec2, #of visits]
- if u have internet connection and permission u can use it from anywhere
- some metrics are gathered natively by the product, anything running in AWS,
  cpu utilitzation by ec2 gathered by default
- some type of metric collection need an extra piece of software called the CLoudWatch Agent
  ex: to collect metrics outside of aws in other cloud env or within on-premise.
  monitoring certain things inside products which aren't exposed to AWS need the Agent.
  ex: monitor which process run on ec2 or its mem utilization
   - CloudWatch Logs - AWS products, Apps, on-premises  
   - CloudWatch Events - AWS services & Schedules.
    - ec2Termination event .. - u can configure listeners to it
  can configure action based on events
  ***
  data generated from aws products & custom data feeded to CloudWatch Metrics
  - generate statistics consumed by API & console
  - Alarm & events to SNS / autoscaling
  - CW architecture:
    - <img src="images/cw-arch.png" width="800" height="500">

---

Namespace
- container for related metrics
- aws data go to AWS namespace AWS/name-of-the-service AWS/EC2
- namespcae contain data of related metrics
  - instance generate data to metric as Datapoint[timestamp, value]
    Dimension separate datapoints for different things or perspective within
    the same metric.  {multiple ec2 send data to the same cpu utilization metric but with different dimension}
    when EC2 sending datapoint to cloudwatch. AWS send also the instance id and type.
    and this allow us to see metric for a particular instance id.

Alarm can be configured to take action based on metrics state .
Alarm states -> OK, Alarm : SNS or action, insufficient data[initial state].

---

Shared Responsibility Model:

- AWS responsibility for security of the cloud
  [Hardware/AWS global infra + services + software run on those svc by aws ]
- Customer responsibility for security in the cloud
  [client-side data encryption, Integrity & authentication,
  server-side encryption (file system And/or data), Networking traffic protection,
  os, network & firewall configuration,
  platform - applications - identity & Access management,
  Customer Data]
- <img src="images/shared-reponsability-model.png" width="800" height="500">
---

HA[high availability] vs FT[fault tolerance] vs DR[disaster recovery]  
HA:

- aims to ensure an agreed level of operational performance,
  usually uptime, for a higher than normal period.
  - ha isn't aiming to stop failure , ha system is designed to be online and providing services as often as possible. replacing failed component by automated process as fast as possible.
  maximizing system up time.
  - system availability is expressed in form of percentage of uptime. 
  fixing crashed server quickly can be done by swapping out
  the compute resource, probably a virtual server. Rather using
  time to diagnose the issue, if u have a process ready to replace it,
  it can be fixed quickly and probably in an automated way.
  - u can also have two servers one active, and one stand by. and
    when failure happen redirect the traffic to the stand by server.
    - when migrate to the standby server, they might have to re-login
      or might have small disruption. fo HA, user diruption is not ideal
      but, is okay. it can happen because main goal of HA is minimizing the outage.
- ha is about keeping a system operational. it's about fast or automatic recovery
  of issues. it's not about preventing user disruption. while that's a bouns, a ha system
  can still have disruption to ur user base when there is a failure.
- ha has costs required to implement it. it needs some design decisions to be made
  in advance. and it requires certain level of automation.
- needs redundant servers or infra to be in place ready to switch

---

FT: 

- is the property that enables a system to [continue operating properly] in
  the event of the [failure of some] (one or more faults within) of its components.
- it means is has to continue operating through a failure without impacting customers.
- HA isn't enough for life threaten app, FT system are designed to work through failure
  with no disruption.{active standby architecture} we might have have the system interacting with 2 servers at the
  same time in active, active configuration. if a server failed, it would drop down
  to just communicating with the server. we can also take it further by replicating
  the client end. so if one server fail or one client fail the system still functional
  correctly and the service would continue uninterrupted.
  \*HA is for increasing up-time, FT to still work through failure
  FT is more expensive and complex to implement.
  plane vs car
  plane replicate the engines and system compnents to tolerate any fault
  car: it's possible to stop until recoverying from failure.
- implementing FT when we only need HA is wasting money and time.
  implementing HA when u need FT, u're putting life at risk.
  ***
  DR:
  disaster recovery: a set of policies, tools and procedures to enable the recovery or
  continuation of vital technology infrastructure and systems following a natural
  or human-induced disaster.
  plan in advance if ur HA & FT don't work.
  backup unreplaceable data in different sites.

---

Identity policies(also know as policy documents)[json]
  - (Sid : statement id, effect: Allow|Deny, resource, action: READ|WRITE):

- get attached to identities inside AWS
- set of security statements to AWS, 
  -it grants access or deny access to AWS products and features to any identitiy which use that policy.

- rules: 
  - explicit deny 
  - explicit allow 
  - Default Deny(implicit) 
- [deny, allow, deny]

- except the root account, aws identity start off with no access to any  AWS resources. if they're not allowed access, they have no access.

- inline policy vs managed policy[reusable, low management overhead]:
  - instead of given each member identity it's own policy(inline), we create one managed  policy and attach that policy to any identity. managed policy fit when u have set  of common access right to different identities.
- inline policy used in exceptional allow or deny.

---

- IAM Users are an identity used for anything requiring long-term AWS access e.g. Humans, Application or service accounts.

- Principal: represents an entity trying to access an AWS account. [initially unidentified]
  - need to authenticate against an identity within IAM. prove to IAM that it is an identity  that it claims to be.
    - principal goes through the auth process --> authenticated identity

# ARN amazon resource name:

- uniquely identify single resources within any AWS accounts.

  - arn:partition:service:region:account-id:resource-id
  - arn:partition:service:region:account-id:resource-type/resourc-id
  - arn:partition:service:region:account-id:resource-type:resource-id

- ex:
- arn:aws:s3:::cargifs <-- identify bucket {access & action}
- arn:aws:s3:::cargifs/\* <-- identify objects in the bucket

some field can be ignored bec

---

# IAM USERS [EXAM POWERUP]:

- 5000 IAM Users per account
- IAM user can be member of 10 groups.
- This has a system design impact.
- IAM Roles & Identity Federation fix this
- Internet-scale application
  - use federation or user Roles.

---

# IAM groups:

- IAM Groups are containers for Users, they exist to make organizing large sets of IAM users easier.
  - u cannot log into a group.
  - iam group have no credentials on their own.
- no limit on how many members can be in the groups.. but there is a limit for: 
  - #of IAM users in the account -> 5000 .
- No Nesting : no groups within groups.
- limit 300 groups per account. but this can be increased with a support ticket.
- IAM user can be a member of multiple group.
- groups can have policy attached to them: Inline, Managed.
  - aws merge all policies that atttached to the user or group the user in. remeber : deny, allow, deny.
- there is a built in all-user-group inside aws iam: <- check google

- resources policy can reference identities. so for example, a bucket could have
  - a policy associated with that bucket and that policy could allow [user] access to that bucket. it controls access to a specific resource and allows or denies identities to access that bucket. 
  - it does this by referencing these identities, and it reference these identities using ARN 
    - Users and IAM roles can be referenced in this way. 
  - so a policy on a resource can reference IAM Users and IAM roles by using the ARN. so bucket could give access to one or more users or it could give access to one or more roles.
  - <b> But, Groups are not a true identity. they can't be referenced as a principal in a policy. </b>
    - a resource policy cannot grant access to an iam group..
  - iam groups allow permission to be assigned to them and users in these groups inherit those permissions.

---

# IAM Roles:

- assigned to multiple aws users, services 
- inside or outside the aws account [unknown number]
  - if u cannot determine the #of principles who will use the identity then IAM ROLE is a good  candidate.
  - or we have mor than 5000 principles[limit for IAM USERs], IAM ROLE is a also a candidate.

- IAM ROLES are assumed.. u become that role for a period of time then stop.
- IAM ROLE don't identify a specific user, it just provide a certain level of permission inside the AWS account for a short period of time.

- roles are real identity and can be refrenced within the resource policies any thing assume the role can access what the role can access.

- IAM ROLE has 2 policy: 
  - Trust policy and permission policy.
    - Trust Policy
      - specify identities can assume that role. \* can specify users or service inside the account or outside. <br> and even can allow an anonymous usage of that role and other type of identity such as fb, google, and twitter. 
        - if a role get assumed for entity in the trust policy, AWS generate temporary security credentials by STS[secure token service] sts:AsumeRole, <br>and these are made available to the identity which assumed the role. [ temporary security credentials] are like access keys[time limited]. 
        - after expiration of the credentials. the identity need to renew them by assuming the role.
    - Permissions Policy: 
      - every time temporary credentials is used , the access is checked against this permission policy. 
      - what is allowed to be accessd by the (IAM ROLE)  in the trust policy.

  - roles are being used within aws organization to allow us to log into other accounts in the organization without having to log in again. 

- when to use: 
  - aws services run on your behalf and they need access rights to perform certain actions.

- AWS Lambda[start/stop EC2, do real-time data processing]. 
  - so function invokation need to have the permissions to perform.
  - rather than hardcoded aws access key in the code we can a role known as (Lambda Execution Role) which has a trust policy <br> which trust aws lambda.
    - lambda can assume that role.
    - permission policy: grant access to the needed services.
  - when execute: 
    - 1- assume role [sts:AssumeRole] generate credentials
    - 2- access the resources using the temp credentials
  - if u don't use the role u will hardcode aws access key instead, it's a security risk, <br>  u will need to change them if u rotate the access key.
  - u don't know the #lambda so it's a good candidate for AWS ROLE.

  <hr>

- Break Glass For Key

  - when the default conditions require a certain level of permissions, <br>    and u want to provide more in emergency situation. u can define role for <br>   that. and the role is assumed when it is required for that situation.
    - the access to that role will be logged.
      - so u'll know if it misused.

<hr>

- we have co-operate with existing identities and we need to offer SSO, <br> or we have more 5000 identities.
  - external identities cannot be used directly in aws. 
    - we need to allow IAM Role inside aws account to be assumed by one of the external identities, after sts:assumeRole they can know access the resources  defined in the permission policy. [ID federation].
      - u manage small #accounts|Identities{iam role} and many account can access.
    - it's one of a major use cases to IAM ROLE.

<hr>
- developing mobile app with many users.  
  - when the app need to store or retrieve data from aws service [DynamoDB],<br>  we cannot create IAM USER for each app user. we can fix it using process ,<br>  called (web identity federation). 
    - use IAM ROLE.

  - if u use web identities [google, fb, twitter] we can allow these identities
    to assume an IAM ROLE based on ROLE TRUST POLICY.
    - advantages: 
      - no aws credential on the app, can scale, using existing customer login.
      - u can scale to 100kk's of accounts
<hr>
- Cross Accounts Access:
  - if u want users from other AWS account to access service from ur account, <br>   u can create IAM ROle for that. and the accounts from other account can assume it.
---

# Service-linked roles:
- special tye of iam role
- IAM ROLE linked to a specific AWS service
  - provide set of predefined permission by a service.
    - providing permission that a service needs to interact with other Aws service on ur behalf.
  - service might create/delete the role or allow u to during the setup or within IAM
- You can't delete the role until it's no longer required.
  - <img src="images/iam/service-linked-role-mgmt.png" >
  - <img src="images/iam/service-linked-role.png" >
---

# AWS organization:

- without it, each account will have its own IAM users, and its own payment method.
- how to manage many aws accounts.
  - each one will have its own pool of IAM users, separate payment method.
- u take standard AWS account[not in organization], u create AWS organization.
- the standard account. become now the management account(previously Master).
- using the management account. u'll invite other AWS accounts to join the organization
  those standard accounts if accept the invite become members account.
  - u can group accounts inside the organization [organization units]
  - so u can build a complex hierarchy of accounts.
  - organization root -> container of accounts not in ou.
  - if u exceed #accounts in the organization, u can ask AWS support to increase the limit.
- individual billing information for each member accounts is removed
  and sent to the management account. [Consolidated Billing].
  - remove financial admin overhead, certain services get cheaper when u use it
    more. and for certain services when u pay in advance u get a cheaper rate.
- servic control policy: specify what each member account can do. restrict the
  service that can be used by the member account.
- using management account u can directly create new member AWS account
  instead of inviting existing AWS account.

- best practice: u don't have to have iamuser in each member accounts, instead
  IAMROLE can be used to allow IAM USER to access other aws memeber account.
  u have a single account to log into, and use IAM ROLE to access the others.

  - we log in to this account and using ROLE Switch to access the others.

<br>
<br>

- Service Control Policies
  - accounts permissions boundaries
  - they limit the account (including account root user) can do.
    - u can never afffect the account root user, but if u restrict the account itself u also affect the root user.
  - feature of organization to restrict members account
  - they don't grant any permissions, only restrict.
  - json document can attach to : 
    - the root to affect all acc in the organization or
    - can attach to a specific OU or to individual account.
  - <b> management account cannot be restricted by SCP. </b>

  - u can use it in 2 ways : {block by default and allow certain services or allow by default and block certain services}
  
  - Allow list vs Deny list
    - Allow list : 
      - FullAWSAccess: allow access to all svc by default and u deny what u want.

    - deny all
      - remove FullAWSAccess
      - ![](./images/iam/scp-deny-all.png)
    - must go for deny all because aws will add new services. deny all[]
      - explicit allow services.
    - to reduce admin overhead of allowing each required service u can allow all and deny
      what u want.
  - only allowed service in both Identity policies in account and SCP will be allowed.
    - <img src="images/iam/effective-permissions-scp-id.png">

---

# CloudWatch Logs
  - public service 
    - usable from AWS or on-premise. -> assuming u have permission and network connectivity.
  - store, monitor and access logging data [information + timestamp]
  - AWS Integrations with 
    - EC3, VPC FLow logs, Lambda, CloudTrail, R53 ....
    - any services which integrate with cloud logs can store data directly inside the product.
  - can integrate with outside application by setup an agent, development kit.
  - can generate metrics based on logs 
    - <b>metric filter</b>.
  - regional service.
  - logging source inject data as log events. -> stored in log stream
  - log stream : sequence of log events from the same source.
    - each log stream is ordered set of log events for specific source for specific thing.
  - Log group: container of multiple log stream from the same type.
    - store configuration settings: Retention & Permissions.
      - apply on all log stream in the group.
        .
    - store metric filter{constantly reviewing any log event for any log stream in that log group looking for certain pattern . ex: app error code} --> metric --> fire an alarm based on the value.

<hr>

- CloudTrail [regional]

  - log api actions which affect AWS Account.
    - terminating EC2 instance, create / delete S3 bucket ...
  - log actions as CloudTrail event.
  - by default store Event of last 90 days.
  - To customize the service .. create 1 or more Trails.
  - we have two type
    - Management Events[log of action on aws resource (control-plane)].
    - Data Events(usage of the resource. upload/access object in S3).
  - by default store Management Events only.
  - ![cloud-trails](images/cloud-trails.png)

  - CloudTrail trail : unit of configuration within CloudTrail, it's a way u provide configuration to cloudtrail on how to operate, CloudTrail log <br>  events belonging to its region. u can configure trail to operate on its region, or to operate on all regions[think of it as trail for each region but working as one].
    - global service log its event to trail for all regions. [IAM, STS, CLoudFront]
      - enabled in created trail by default.
    - u can configure a trail to store in a specific S3 to persist it forever.
      - stored in json u can parse a specific information u want.
      - can integrate with (cloudwatch log) and store/monitor data there.
    - u can create a trail for all accounts in the organization. [organizational trail]

<br>

- Exam powerup:
  - cloud trail enabled by default for 90 days for free.
  - trails are how u configure S3 and CWLogs.
  - Management events only by default.
  - IAM, CLoudFront, STS => global services events [us-east-1]
  - NOT REALTIME 
    - There is a delay[~15mins].

---

S3 -- S3 is private by default

- the only identity which has any initial access to an S3 bucket is
  the account 'Root User' of the account which own that bucket.
  S3 Bucket Policies:
  - Bucket policies are a type of AWS resource policy.
  - like identity policy, but instead they attached to a resource
    instead of identities, u control who can access that resource.
  - idenetity perspective : u can only attach identities inside ur aws account.
  - Resource perspective permissions
    - Allow/DENY same or different account, anonymous principals.
      we can reference all principals in the world.
      --see examples of bucket policies---
      main keys in the statement. ["Sid", "Effect[Allow|Deny]",
      "Principla" not exist in identity policy, "Action", "Resource[arn]"]
    - another keys "Condition" <-- policy apply if cond is true.
  - on bucket policy on a bucket, but it can have multiple statements.
  - access right for identities in the account is the combination of the resource
    polices and any identity policies attached to the account.
    - for anonymous principle, resource police only apply. [because no identity]
    - for cross acount access, [identity policy in their account must allow
      access to the bucket in ur account and the bucket policy must also allow them]
- ACL access control list -- [legacy]
  - can apply on objects & bucket -- cannot select a group of them
  - a subresource , legacy
  - inflexible & simple permission [READ|WRITE|READ_ACP|WRITE_ACP|FULL_CONTROL]
  - <img src="images/acl-s3.png" width="800" height="500">
- Block Public Access:
  - added in respone to lots of public PR disaster, where buckets were being configured
    incorrectly and being set open to the world. this resulted in a lot of data leaks.
  - ex: bucket with resouece permissions granting public access, until block access
    was introduced, if u have public access configured the public could logically
    access a bucket.
    Block public access added a further level of security. apply to pervent
    the public access no matter what the resource policy say. [4-options]
    not apply on any aws identities

Key points[identity, resource policy]:
- identity: controlling different resources, u have a preference for IAM, same account.
- bucket: just controlling S3, anonymous or Cross-account

---

S3 Static Hosting:

- normal access[CLI, UI] is via AWS APIs.
- this feature allows access via HTTP - e.g Blogs
- enable it, set Index and Error documents.
  - website endpoint is created. influenced by the bucket name
and the region that the bucket is in
- to use custome domain name via R53 
  - bucketname matters. u can use it it the name of the bucket match the custom domain.
- two use cases:
- Offloading
  - server run on compute service [ec2], delivers a dynamic HTML page and
    it deliver static media.
    dynamic may need access to DB so that's not suitable for static s3 hosting.
    but the static media are sitting there waiting to be delivered. [make over 95%
    of the data volume]
    we can move all the static media from the ec2[which is expensive] to static S3.
    when ec2 deliver the dynamic generated html which point at the
    media that hosted on the S3 bucket to customer browser.
    so the media is retrieved from S3, not the compute service.
- Out-of-band pages
  - accessing something that is outside of the main way.
  - store error page/static on it, so when the compute service face an issuse.
    we redirect customer to it{change where the domain is pointing to} .
- Pricing on [put, get, copy]requests, storage, data out.
  -beaware if u have large base of customers {because u will recieve a lot of requests}
move data from internet to s3 is free.

- DEMO
  - unblock public access
  - if u  have a domain name, u can use it.
    - {bucketName}.{yourDomainName}
    
  - properties -> static website hosting -> enable
  - upload index.html, error.html, and other assets.
  - add bucket policy to allow public access.
    - GetObject action on the website objects{index.html, error.html, assets..} / or all /*
  - error.html will be displayed if the object is not found.
  - to customize the url
    - go to r53 -> hosted zones -> choose the domain u write in the bucket name
    - create record -> simple routing -> choose the bucket name, set the other options to match the bucket info.

<hr>

# Object Versioning

- controlled at a bucket level.

  - start at disable.
  - once enabled cannot be disabled.
  - <img src="images/object-versioning-states.png" width="800" height="500">
  - we can suspend the bucket but we can enable it back.
  - without versioning modifying object will replace the object.
  - lets us store [multiple versions] of objects within a bucket.
    operation which would modify objects generate a new version.
  - attribute id=null, when versioning is disabled.
  - when enabled for each object aws allocate id to objects.
  - when u access object without specifying its id, u will get the latest version.
  - when delete, S3 add delete marker to hide all versions of specific object.
    - if u delete the latest version, the previous version will be the latest.
  - but u can undo the operation.
  - if u want to fully delete, specify the object id and this version will be fully deleted.
  - u will be charged for all versions size of the object even u suspend object versioning.

---

MFA delete

- enabled in [versioing configuration]
- MFA is required to change bucket [versioning state].
- MFA is required to delete any version of an object.
- to fully delete any versions, u need MFA token.
- Serial number (MFA) + code  to delete version or change bucket version
    is concating with any api call.

# S3 Performance Optimization{it's often about performance and reliability}:

- Single PUT Upload[default]:

  - Single data stream to S3 , if stream fails - upload fails, require full restart.
  - Speed & reliability = limit of 1 stream.
  - single put upload limit to 5GB of data as maximum.

- Multipart Upload
  - Data is broken up
  - Min data size 100MB for multipart
  - broken to 10k max parts, 5MB -> 5GB. last piece can be smaller
  - Parts can fail, and be restarted.
  - transfer rate = speed of all parts.
  
- S3 Accelerated Transfer(OFF by default)
  - use network of aws Edge Location
  - restrictions to enable it:
    - the bucket name cannot contain periods, and it needs to be DNS compatible
      in its naming.
  - when upload data to S3, instead going to it directly, it immediately enters
    the closest best performing AWS edge location
    - reaching edge location occur over public internet, but geogeraphically its really close.
      - Edge location transit the data over AWS global network which is directly under the
        control of AWS. tends to have a direct link between these lcoations and other areas
        of the AWS global network, in this case, the S3 bucket.
    - public internet is not designed for speed, it is designed for flexiaility and resiliance.

---

# presigned url

- a way to give another person or app an access to s3 obj using ur credentials in a safe and secure way{encoded in the url}.
  - iam identity create a presigned url, which is a url that contains a signature. it has a time limit. and it indicate the operations that can be performed on the object.
  - presigned url can be used to upload or download an object in a private s3 bucket.
  - <img src="./images/presigned-url.png" width="850" height="500">

- exam powerup:
  - <img src="./images/presigned-url-exam-powerup.png" width="850" height="500">

---
kms key precviously called CMK
Key Management Service(KMS)

- Regional & public service.
- occupies AZ public zone and it can be connected to from anywhere with access to
  this public zone. [if u have permissions to access it]
- Create, Store and manage Keys.
- handle [Symmetric ] and [Asymmetric Keys]
- perform Cryptographic operations(encrypt, decrypt...)
- [Keys never leave KMS] 
- Provide FIPS 140-2(Level 2) {try-to-remember}
- KMS can create keys, keys can be imported, it manages keys, it can use these keys
  to perform operations, but keys are locked inside KMS.
  Customer Master Key(KMS key)
- used KMS within cryptoghraphic ops, u can use them, applications can use them
  and other AWS services can use them.
- KMSs are logical - containers of the actual physical master key.
  contains [ID, date, key policy, desc & state]
  ..backed by physical key materials.
  these physical material can be generated by KMS or imported into KMS.

- when u ask kms to decrypt data, u don't need to specify the key to use, kms
  will use the correct key to decrypt the data. that info is encoded in the ciphertext.
  - <img src="./images/kms.png" width="850" height="500">
  - permission for encryption and decryption are different.
  - this material contained inside a kms can be used by the KMS product to directly
    encrypt or decrypt data - KMS keys can be used for up to 4kb of data.
    --
    KMS encrypt the KMS key [CreateKey] before it's stored persistently on disk.  
     when we wanto encrypt data we send it to KMS, which decrypt the KMS key and use it to
    encrypt data and return it. in decrypt operation KMS can use the correct KMS key because
    that information is encoded into the ciphertext of the encrypted data by kms.

    to get around limit of 4kbs in KMS key

# Data encryption keys(DEKs)
- aother type of key which KMS can generate, and generate it using KMS key.
- GenerateDataKey - works on > 4kb
- links to specific KMS which create them
- KMS does not store the DEK in anyway:
  - it provide it to who use it and then discards it. because KMS doesn't
    - perform encryption or decryption of data using the DEK. the user do that.
      -  because kms doesn't use dek in encryption or decryption,  u do.
    - KMS doesn't use DEK for anything beyond generating them .
- after KMS create it, it provide to u 2 versions of the key:
  - plaintext version 
  - ciphertext version[encrypted be the kms].
- Discard it.
- <img src="./images/dek.png" width="850" height="500">

-user of the service use the plaintext of KMS key encrypt the data and
store the encrypted data and encrypted version of the key. - when user want to decrypt data- > send the encrypted DEK) to kms to
decrypt it and return the plaintext of the key to use it.


S3 use KMS: - generate DEK for every single object, encrypt the object and discard the plaintext
version .
- Key points for KMS in the exam: 
  - KMS keys are stored within KMS specific to a region & never leave.
  - <img src="./images/kms-key-concepts.png" width="850" height="500">
- u can't directly deal with KMS keys, u must use APIs available from KMS. 
- Aws managed KMS key or customer managed KMS keys 
- AWS managed KMS keys are created automatically by aws when use a service such as S3
  which use KMS for encryption. 
- Customer managed key are created explictly by the customer.
  - much more configurable :
   - u can edit key policy, allow other AWS accounts access the KMS key to perform ops 
   - KMS key support key rotation .. physical backing material 
   - data that's used to actually do the cryptographic operations is changed. with AWS KMS key managed key  it always enabled for every 1095 days. if u enable it for customer KMS key it will happen
  every one year. 
  - KMS key itself contains the current backing key, and previous backing keys  [the physical material]  - caused by rotating that material. 
  - u can use Aliases[per region] to KMS keys. u can point to the KMS key alias and change
  the KMS key beneath it as required.

- kms and permissions:
  - most service in aws trust the aws account root user except kms need to explicitly add the account to the key resource policy.

--
Key Polices and security

- key policy (resource)
- every KMS key has one! --> need to explicitly set who can access the key
- Key policies + IAM policies.
- <img src="./images/kms-policies-permissions.png" width="850" height="500">

---

# S3 encryption:

- Bucket aren't encrypted .. objects are.. ..bucket default encryption is different.
- each object inside the bucket could use a different encryption settings.
- S3 support [Client]-side Encryption and [Server]-Side Encryption.
  - both of these refer to encrption at rest.
- method of determining how objects are encrypted, when they're persistently stored on disk
- it doesn't refer to encryption in transit. [ encryption in transit] come standard with s3. 
- <img src="./images/s3-encryption.png" width="850" height="500">
- customer-side
  - u are responsible for managing and performing the encryption and decryption of data.
- Server-Side Encryption:
  - Server-Side Encryption with Customer-Provided Keys(SSE-C)
    - customer is responsible for encryption key.
    - s3 manage the encryption and decryption process.
    - s3 store the encrypted object and the hash of the key.
    - so u need to provide the object and the encryption key to s3  .
    - a hash of the key is attached to the object to check the key when u want to decrypt it.
    - s3 store the encrypted object and the hash of the key. and discard the key.
    - u offloading the encryption and decryption process to s3.
  - Server-Side Encryption with Amazon S3-Managed Keys(SSE-S3 AES256)
    - aws s3 and the operation and the key management.
    - u send data, s3 generate root Key to use[key for each object]. it's invisible to u.
    - s3 generate key for every single object. and then encrypt the object with that key.
      - then s3 encrypt the key with the root key and store it with the object. and the key is discarded.
    - not suitable option for role separation. when u need some to manage the infrastructure and some to manage/access the data.
  - Server-Side Encryption with KMS keys stored in AWS KMS (SSE-KMS) 

    - generating key by KMS, DEK 
    - default master key KMS key 
    - this KMS key used to generate a unique DEK for each object/ 
    - u can use customer managed KMS key
    - benefits[separation of roles] 
    - if u have permission on S3 to manage it, and u don't have access to KMS
    - u cannot decrypt the data inside S3.
- summary:
  - <img src="./images/s3-encryption-summary.png" width="850" height="500">
  # Bucket default encryption:
  - data ---PUT---> S3
  - u specify header [ x-amz-server-side-encryption ] if u don't the object isn't
  be encrypted. 
  - if u don't set the header the default for bucket will be used.
  - if u configure the bucket with default val it will be used.
  - if u set it to AES256 SSE-S3 will be used, aws:kms -> SSE-KMS
  - u can use bucket policy to restrict the possible encryption options.
  ***


# S3 Object Storage class
# S3 standard
    - object store across at least 3 AZs in the aws region.
      return HTTP/1.1 200 OK
    - provide 11 9s durability for 10m objects, 1 object loss per 10k years.
    - Replication over 3AZ's & Content-MD5 Checksums and Cyclic Redundancy Checks (CRCs)
      are used to detect and fix any data corruption.
    - billed GB/m fee for data stored. No specific retrieval fee, no minimum duration, no
      minimum size.
    - S3 standard has a 'milliseconds' first byte latency and objects can be made publicly
      available.
    - used for frequently accessed data which is important and Non replaceable.
    - <img src="./images/s3-standard.png" width="850" height="500"
# S3 standard-IA infrequent access.
  - same as S3 standard but,
    a new cost component, which a retrieval fee for each GB of data retrieved from
    the product there is the cost + transfer fee.
    objects can be stored for less, but the minimum biling always applies.
  - minimum duration charge of 30 days - object can be stored for less, but minimum
    biling always applies
  - standard-IA has a minimum charge of 128kb per object. not prefered class if u have
    lots of tiny objects.
  - cost effect as long as u don't access the data very often, or u don't need
    to store it short term, u don't need to store lots of tiny objects.
  - <img src="./images/s3-standard-ia.png" width="850" height="500">
# S3 One zone-IA
  - data replicated but inside on Az
  - used for long-lived data, which is Non-critical & replaceable and where access
    is infrequent -> replica copies if u're using same or cross-region replication,
    then u can use this class for your replicated copy. or if u're generating intermediate
    data that u can afford to lose,
  - <img src="./images/s3-one-zone-ia.png" width="850" height="500">
# S3 Glacier - Instant
  - 3AZ
  - minimum duration charge of 90 days.
  - a minimum capacity charge of 128kb
  - instance access as S3 standard but cost increase in frequent access.
  - <img src="./images/s3-glacier-instant.png" width="850" height="500">
# S3 Glacier - flexiable
  - 3AZ
  - 11 9s durability.
  - 1/6 cost of s3 standard retrieval.
  - Objects cannot be made publicly accessible .. any access of data
    (beyond object Metadata) require a [retrieval process].
    - retrieval process: a job u need to run, u pay for it.
      when u retrieve the object is stored in S3 IA on a temporarly basis
      u access it then object will be dropped.
    - Data in [glacier flexiable] is retrieved to s3-ia temporarily
      - Expedited (1-5mins)
      - standard (3-5hrs)
      - bulk (5-12hrs)
        ** faster = More expensive
        **first byte latency = mins or hrs!!
  - 40kb min size, 90 day min duration.
  * suitable for archival data where frequent or realtime access
    isn't needed (e.g. yearly) mins-hours retrieval.
  - <img src="./images/s3-glacier-flexiable.png" width="850" height="500">
# S3 glacier Deep Archieve
  same as [flexible] but:

  - standard (12hrs)
  - bulk (up to 48 hrs)
    \*\*first byte latency = hours or days!!

  - 40kb min size, 180 days min duration.

  - not suitable for backup
  - suitable for archival data that rarely if ever needs to be accessed -
    hours or days for retrieval. e.g. Legal or Regulation data storage
    in term of Retention length.
  - <img src="./images/s3-glacier-deep-archieve.png" width="850" height="500">

# S3 Intelligent-tiering

  - contain 5 different storage tier:

    - frequent access tier. _like_ s3 standard
    - IA tier.
    - Archieve instanc access tier. [up to 90 days]
    - Archieve access tier. [90 -> 270]
    - Deep archieve tier. _glacier deep archieve_ [180 or 730]
      u don't have to worry about moving data between tiers, AWS
      handle that according to how many the object accessed.

    - cost: Intelligent Tiering has a monitoring and automation cost per {1000
      objects}. the frequent access tier costs the same as S3 standard, the
      infrequent the same as Standard-IA. Archieve Instant, Archieve and Deep
      Archieve are comparable to their S3 glacier equivlents.

    - As objects are accessed, they are moved back to the frequent access tier.
      there are no retrieval fees for accessing objects.

    - S3 Intelligent-Tiering should be used for long-lived data, with
      changing or unknown patterns.
    - <img src="./images/s3-intelligent-tiering.png" width="850" height="500">

<hr>

# S3 Lifecycle configuration 
  - u can create Lifecycle rule on s3 bucket which can automatically
expire object in the bucket. great way to optimize the cost of S3. 
  - set of rules, consist of [actions] do x if y is true. 
  - can be applied on a Bucket or groups of objects. 
    - Transition Actions: change object class [S3 standard -> S3-IA] 
    - Expiration Actions: Delete object or a specific version. 
      - actions on version is more complex.

S3 Standard
S3 Standard-IA
S3 Intelligent-Tiering
S3 one AZ
S3 Glacier - Instant retrieval
S3 Glacier - Flexible Retrieval
S3 Glacier Deep Archieve

- lifecycle rules can move object downward not upward.
- all can move data down to any lower class, but S3 one AZ
cannot move objects to S3 glacier - Instant retrieval {be aware of that exception}.

- A single rule cannot transition to Standard-IA or One Zone-IA. and then
to glacier classes ... within 30 days (duration minimums) 
  - u can have 2 rules without 30 days gap.

- in single rule to move from s3 to [s3-ia or S3-1-AZ-IA] rule applied after 30 days,
and to move with the same rule to galcier class u need to wait another 30 days.



- smaller objects can cost more on classes under standard because (minimum size).
- <img src="./images/s3/s3-lifecycle.png" width="850" height="500">
--------

# S3 Replication
- a feature that allow u to configure a replication of obj between a source and destination.
- CRR (Cross-Regin Replication) : replicate obj between 2 buckets in different regions.
- SRR (Same-Region Replication) : replicate obj between 2 buckets in the same region.

- to replicate between two different accounts u need to add a bucket policy to the destination bucket, 
  to allow the source account to replicate to it. beacuse the s3 in  destination account don't trust the source account{role}.

- <img src="./images/s3/s3-replication.png" width="850" height="500">

- options
  - All objects or a subset of objects.
  - Storage class of the destination. default is same as source.
  - Ownership of the object. default is same as source.
  - Replication Time Control (RTC) 
    - add gurantee that the object will be replicated within the 15 mins.

- consideration for exams
  - Not retroactive. only new objects. & versioning needs to be enabled.
  - One-way replication. src to dest
  - it can handle Unencrypted, SSE-S3, SSE-KMS(with extra config)
  - src bucket owner needs permissions to obj.
  - NO system events, Glacier or Glacier Deep Archieve.
  - DELETES are not replicated.

- why use replication...?
  - SRR - Log Aggregation
  - SRR - PROD and TEST Sync
  - SRR - Resilience with strict sovereignty requirements.
  - CRR - Global Resilience improvements
  - CRR - Latency Reduction
--------
# S3 Select and Glacier Select
- <img src="./images/s3/s3-select.png" width="850" height="500">
- comparing retrieving the whole object vs retrieving a subset of the object.
- <img src="./images/s3/s3-comparing-select-with-normal-retrieving.png" width="850" height="500">
--------
# cors
- <img src="./images/s3/s3-cors-for-exam.png" width="850" height="500">
---------
# S3 EVENTS
- allow u to create event notification configuration on  bucket.
- s3 events u can listen to 
  - <img src="./images/s3/s3-event-types.png" width="850" height="500">
  - example 
    - <img src="./images/s3/s3-event-flow.png" width="850" height="500">
-------
# S3 Access log
- we have a src bucket and target bucket.
  - target bucket is where we want to store the access logs.
  - src bucket is where we want to enable access logs.
- we need to enable access logs on the src bucket.
  - using console ui, or via PUT bucket logging API.
- logging is managed by s3 log delivery group which read the log config u set on src bucket.
- it's a best effort feature 
  - any change in config take hours to take effect.
- u need to get s3 log delivery group the permission to write to the target bucket.
- logs delivered as log file, each file consist of log records.
- u need to personally manage the deletion of the log files.
- <img src="./images/s3/s3-access-log.png" width="850" height="500">
-------
# S3 Requester Pay
- shift the responsibility for paying for data transfer charges out of aws tp the requester.
  - <img src="./images/s3/s3-requester-pay.png" width="850" height="500">

--------
# S3 Object Lock
- Object Lock enabled on `new` buckets*(support req for existing)
- Versioning is also enabled on that bucket. once enabled it cannot be disabled.
- write once read many (WORM) model. No Delete, No Overwrite.
- 2 modes
  - Governance Mode : users can't overwrite or delete an object version or alter its lock settings unless they have special permissions.
  - Compliance Mode : a protected object version can't be overwritten or deleted by any user, including the root user in your aws account.
- 1 Retention Period
  - specify DAYS & YEARS - A Retention Period
    - COMPLIANCE - can't be adjusted, deleted, overwritten. no change to obj ver or retention period settings. even by root user.
      - medical or financial records.
    - GOVERNANCE - can be adjusted, deleted, overwritten. but only by users with special permissions.
      - legal or business records.
      - s3:BypassGovernanceRetention
      - x-amz-bypass-governance-retention:true (console default) header provided in the request. to chabge the retention period.  

- 2 - Legal Hold
  - u can place a legal hold on an object version to protect it from deletion indefinitely.
  - u can't place a legal hold on an object version that is already locked in governance mode.
  - <img src="./images/s3/s3-legal-hold.png" width="850" height="500">
- <img src="./images/s3/s3-object-lock.png" width="850" height="500">
- it can be used in conjunction with each other legal hold and retention period.
------- 
# Policy Interpretation

- start with $.Statement
  - Effect{Allow, Deny}
    - remember that explicit Deny always wins.
  - Resource
    - is the policy applied to the resource or a sub resource. {bucket, bucket's objects}
  - NotAction
    - any action other than the included ones.
    - ['cloudfront:*', 'iam:*', 'route53:*', 'support:*'] <- global services generally u interact with as they in us-east-1 from logging prespective.
    - deny any action from any region other than {'eu-west-1','ap-southeast-2'} excepting the global service.
      - <img src="images/resource-evaluation-02.png">
  - condition
    - policy only apply if the it's evaluated to true.  
- identify any Not before considering a solution
- if u have single statement with deny. then generally it will be used in conjunction with another policy.  
  - because in it's own it does nothing because the default is implicit deny.
---

# Permission Evaluation
- same account
  - <img src="images/permission-evaluation-01.png">
  - scp check for the account which is containing the identity.
  - session policy -> if u assuming a role.


# CloudHSM
- create, manage and secure cryptographic keys.
- ![cloudhsm](images/cloudhsm.png)
- cloudhsm is not actually deployed inside a vpc u control.
  - deployed in aws managed cloudhsm vpc.
- for HA u need to deploy multiple hsm and configure them as a cluster.
  - once they are configured as acluster they replicate keys ad policies or any other important configuration{u don't need to configure it}
  - hsm by default is not a high available device.

- HSM is injected to ur custom managed VPC via Elastic Network Interface. one for each device.
- ![cloudhsm-usage](images/cloudhsm-usage.png)
- ![cloudhsm-usecases](images/cloudhsm-usecases.png)
---

# VPC : 

- how to design a scalabe network inside VPC.
- Considerations: 
  - What size should the VPC be ... 
  - Are there any Networks [we can't use] ... 
    - overlapping or duplicate ranges will make network communitcating diffcult
    - networks
      - VPC's, CLoud, On-premises, Partners & Vendors 
  - try to avoid ranges which other parts use, which u might need to interact with. 
  - Try to predict the future... 
    - try to think about how many regions ur business will operate in, think about the highest possible number and buffer to it.
      - <img src="images/vpc/more-consideratons.png" width="850" height="500">
  - VPC Structure 
  - Tiers & Resiliency (Availability) Zones. 
    - Tier : separate application components and allow different security to be  applied{web-tier, db-tier} 
  - Networks are often split and part of that network are assigned to each of this AZ.

- custom vpc: a private network in aws.
  - u need to know the range of ip addresses u want to use in advance. 
    - it's a critical point because it cannot be change later.
    - ranges we cannot use in our A4L scenario
      - <img src="./images/vpc/ranges-we-cannot-use.png" width="850" height="500">
  - Regional Service 
  - IGW
    - give resources in VPC public access.
  - NGW
    - give private instances only outgoing access.
  - All AZs in the region. 
  - Isolated network 
  - Nothing <mark>IN</mark> or <mark>Out</mark> without explicit configuration. 
  - if u have one resource or multiple are exploited, the impact is limited to the current VPC and only anything connected to it. 
  - Flexible configuration 
  - Hybrid Networking - 
    - let u to connect to other cloud & on-premises network. 
  - Default or Dedicated Tenancy! 
    - control whether the resource is created inside the VPC are provisioned on shared hardware or dedicated hardware. 
    - if u choose dedicated hardware on VPC level, any resource created on that VPC will be on dedicated hardware. increase cost. be careful, don't select it only if u know what u doing. 
  - IPv4 private CIDR Blocks & [public IP] (when u want to make a svc available for public access).

  - 1 Primary Private IPv4 CIDR block, allocated in creation. 
    - Min /28 (16 IP) Max /16 (65536 IP) 
    - optional secondary IPv4 Blocks. {after creation}
    - optional single assigned IPv6 /56 CIDR Block.
      - the range is either allocated by aws, or u can select to use your own IPv6 addresses which u own. u cannot pick a block.
      - IPv6s are publicly routable by default, but u need to allow it explicitly .
  - <img src="images/vpc/vpc-sizing.png" width="850" height="500">
# DNS in a VPC 
  - Provided by R53 
  - VPC `Base IP +2` Address 
  - {enableDnsHostnames} : 
    - give instances DNS Names .
    - indicate whether public instances inside VPC are given dns public hostname. 
      - if it set to true they get dns hostname.
  - {enableDnsSupport} 
    - enables DNS resolution in VPC 
    - whether DNS resolution inside VPC enable or disable. if e [VPC +2 is available]
    - enableDnsSupport first setting to check if u have dns issue

<br>
<br>

- recommandation for structuring your VPC.
  - 3-subnet + 1 for future
  - 3-tier + 1 spare
    - 16 subnet in /16 network will result in 16 /20 network{4091 IPS}.
  - <img src="images/vpc/vpc-structure.png" width="850" height="500">

<br>
<br>

# Subnets:
- vpc services run from subnet to directly from vpc.
- blue means private subnets, green means public.
- located in one AZ.
- AZ resilient.
  - created in on AZ and it can never be changed
- A subnetwork of a VPC 
  - a part of VPC within a particular AZ.
- 1 Subnet => 1 AZ, 1 AZ => 0+ Subnets.
- IPv4 CIDR is a subset of the VPC CIDR. must be inside the VPC CIDR.
  - Cannot overlap with other subnets.
- Optional IPv6 CIDR (/64 subset of the /56 VPC - space for 256)
- Subnets can communicate with other subnets in the VPC.

# Subnet IP Addressing:

- Reserved IP addresses (5 in total).
- 10.16.16.0/20 (10.16.16.0=> 10.16.31.255)
- first address is a Network address (10.16.16.0)
- Network +1 (10.16.16.1) 
  - VPC Router, logical network device  which move data between subnets and in/out VPC if it configured to allow that
- `Network +2` (10.16.16.2) - Reserved (DNS\*) <- reserved in every subnet.
- `Network +3` (10.16.16.3) - Reserved Future Use
- Broadcast address 10.16.31.255 (Last IP in subnet)
  - NOT SUPPORTED INSIDE VPC, BUT IT RESERVED

<hr>

# DHCP Options Set
- VPC has a configuration object apply to it called a DHCP option set.
    - how computing device recieve ip address automatically. 
    - 1 DHCP option set  apply to VPC at one time.
      - this configuration flows through to subnets .
      -  it control things like DNS service, Ntp services... 
- cannot be updated
 -  u can create new one and change VPC allocation to this new one.
- 2 options u [Auto assign public IPv4, IPv6 ]

---

# bASTION hOST[JumpBox] 
- an instacne in a public subnet. 
- used to manage incoming management connection. 
- access internal VPC resource. 
- used as management point or entry point for private only VPC. 
- often the only way IN to a highly secure VPC.
- can be configured to only accept connection from certain IP addresses. <br> to authenticate with ssh. or to integrate with our own on premises identity server.

---

# VPC Router 
- Every VPC has a VPC Router 
- highly available.
- [move traffic from somewhere to somewhere else]
- Runs in all AZs the VPC uses, - In every subnet .. `network+1` address. 
- the default config : Routes traffic between subnets in that VPC. 
- Controlled by `route table` each subnet has one. 
- specify what to do with traffic when it leave the subnet. 
- A VPC has a Main route table 
  - subnet default. 
  - VPC can only have one Route table, but Route table can associated with many VPC 
  - Target #local means the dest in the VPC itself
  - local routes have higher priority

  
# IGW
- Region Resilient gateway attached to a VPC. 
- 1 VPC = 0{private} or 1 IGW, 1 IGW = 0 or 1 VPC. 
- Runs from within the AWS public Zone. 
- IGW traffic between the VPC and the Internet or AWS public Zone (S3..SQS...SNS..etc) 
- Managed 
- Aws handles performance. it simply works! 
- when attached to VPC we use it as a target in the route table. 
-
<ol>
<li> Create IGW </li>
<li> Attach IGW to VPC </li>
<li> Create Custom RT </li>
<li> Associate RT </li>
<li> Default Routes [0.0.0.0/0 & ::/0] IGW </li>
<li> Subnet allocate IPv4 </li>
</ol>

- if there is no security limitation any services in subnet with public ip can communicate with internet and vice versa.
--

- IPv4 Addresses with a IGW 
  - IGW maintain a recorde mapping [private:public] 
  - os configured with the private ipv4 in the EC2. 
    - OS don't know about the public ip
    - public ip never touch the instance
    - <img src="images/vpc/igw-pub-private.png" width=600 height=320>
  - \*\*\*\*  ipv6 is natively public 
  - IGW manage the translation of the ip dest/src private <-> public.

---
<hr>

- Every `connection` has two parts 
  - Request(initiation) and Response.
  - <img src="images/vpc/in-out-req.png" width=600 height=320>
<ol> 
<li> The client picks a temporary(ephemeral) source port, 1024-65535(Depends On Os) </li>
<li> The Client 'Bobs Labptop' initiates a connection to the server on a well-know Destination port HTTPS tcp/443 </li>
<li> The server responds using source port of tcp/443 and the Destination ephemeral port picked by the client. </li>
</ol>

- Directionality
  - Inbound or outbound depends on perspective.  
  - <img src="images/vpc/directionality.png" width=600 height=320>
# Stateful and Stateless firewall.
 
- stateless firewalls: 
  - it doesn't understand the state of the connection.
    - req & resp for the same are different.
  - see the two part of the connection [Request/Response] 
    - each as a separate connection. allowing or denying them need 2 RULES. 
  - one for each [INBOUND/OUTBOUND] 
  - with stateless firewalls remember the RESPONSE EPHEMERAL PORTS .. so u allow the whole range.
it's NOT THE WELL KNOWN APP PORT. so we have to allow the full
range of ephemeral ports to any destinations.

- stateful firewalls: 
  - stateful means lower admin overhead. 
  - A Stateful firewall is Intelligent enough to identitfy the REQUEST and RESPONSE components of a connection as being related. 
  - ALLOWING the REQUEST (INBOUND or OUTBOUND), means the RESPONSE is AUTOMATICALLY allowed. 
  - no need to allow the full range of ephemeral ports.

---

# NETWORK ACCESS CONTROL LISTs (NACL)
- can be though of as a traditional firewall available within AWS VPC. 
- Every subnet has an associated NACL, filter traffic crossing the subnet boundary INBOUND or OUTBOUND. 
- connections within a subnet aren't impacted by NACLs. 
- contain a #of rules [Inbound/Outbound] rules. 
  - Inbound rules match traffic 
    - ENTERING the subnet, 
  - Outbound rules 
    - match traffic LEAVING the subnet. 

- NACLs are stateless doesn't matter if it's req/res. what is important is the direction. 
- Rules are proccessed in order, lowest rule number first. Once a match occurs, Processing stops. \* is an implicit DENY if nothing else matches.

- Because NACLs are stateless each communication needs 1 REQUEST rule  and 1 RESPONSE rule. 
  - These rule-pairs (app port & ephemeral ports) are needed on each NACL for each communication type which occurs 
    - 1) within a VPC, 
    - 2) TO a VPC 
    - 3) FROM a VPC  
   
# Default NACL
- A VPC is created with a Default NACL
- INBOUND and OUTBOUND rules have the implicit deny(\*) and an ALLOW ALL   rules. <- reduce admin overhead.

# Custom NACLs

- can be created for a specific VPC and are initially associated with NO  subnets.
- They only have 1 INBOUND rule the implicit (\*) DENY.
- They only have 1 OUTBOUND rule the implicit (\*) DENY.
  - ALL traffic is DENIED.  
# RECAP
- [NACL] stateless 
  - REQUEST and RESPONSE seen as different.
- Only impacts DATA CROSSING SUBNET BOUNDARY.
- NACLs can EXPLICTLY ALLOW and DENY.
- Deals with IPs/CIDR, Ports & Protocols 
  - no logical resources.
- NACLs cannot be assigned to AWS resources 
  - only subnet.
- Use together with Security Group[allow] to add explicit DENY (Bad IPs/Nets)
- Each subnet have one NACL (Default or Custom).
- A NACL can be associated with MANY Subnets.

---

# VPC Security Groups (SG) 
- STATEFUL 
  - detect response traffic automatically. 
  - u don't need to allow ephemeral ports.
- Allowed (IN or OUT) request = allowed respone. 
- LIMITATION: 
  - NO EXPLICIT DENY .. Only ALLOW or implicit DENY.  
    - cannot block specific bad actors. 
      - so if u need to deny a specific ip in allowed range u need to use NACLs in conjunction with SGs.
    - Supports IP/CIDR ... and logical resources.
- including other security groups AND ITESELF. 
  - not attached to instances or subnet.
  - Attached to ENI's[Elastic netowrk interface] not instacnes (even if UI shows it this way).
- (SG) Logical Reference: 
  - we have WEB[instances] and APP[instances]. 
  - we allow all inbound traffic in SG of WEB. 
  - WEB need to access APP in [tcp:1337] to allow that in SG of APP? 
    - add the ip of web instances ? add the whole subnet ? both are not

<br>

- taking advantages of (SG) Logical References. 
  - we can reference sg{u now refrence any instances in this sg} to allow inbound traffic on port 1337 from any resources the WEB-SG assigned to it.
  -  <img src="images/vpc/sg-logical-ref.png" width=800 height=320>

- and even when we scale WEB instances the new one can access.
- Self-References:
  - // private subnet in VPC with changing number of resources.
  - <img src="images/vpc/sg-self-ref.png" width=800 height=320>

---

NAT and NAT gateway[giving a private resource an outgoing access to internet]: 
  - A set of processes , remapping SRC or DST IPs.
  - AZ resilient.
  - (IP Masquerading) 
    - hiding CIDR Blocks behind one IP [many private to one public] 
    - Give private CIDR range [outgoing] internet\* access.
    - don't support security groups. only nacls.
  - historical u can use ec2 instance and configure it as NAT.
    - u need to Disable src/dest checks in ec2 instance. to not to drop the traffic.
    - <img src="images/vpc/nat-instance-vs-natgw.png" width=800 height=320>

  - <img src="images/vpc/natgw.png" width=800 height=320>
    - charge per hour Duration & Data Volume.
  
  - IPV6
    - <img src="images/vpc/natgw-ipv6.png" width=800 height=320>
---

====
# EC2
====
- AWS NITRO

---

# Virtualization 101:

- Running more than one OS on one physical machine.
  - TYPES:
    - Emulated Virtualization[SWF]:
      - host OS/Hypervisor manage the [unmodified]guest OS.
      - each privilage call from guest kernel using Binary Translation (on the fly)translated to be made by the Hypervisor.
      - inefficient because hardware doesn't aware of it.
    - Para-Virtualization:
      - each vm run a modified OS for a specific Hypervisor.
      - these modified part instead of making a privilaged call, make a [hyper-call]: calling the hypervisor rather than the hardware.
      - more efficient than Emulated OS because now the host OS aware of it.
    
    - Hardware assisted V:
      - hardware aware Virtualization, cpu provide a special intructions for Hypervisor to manage VM.
      - need to manage access to the devices, not effiecient in case of I/O intensive workload. unless u have physical nic for each VM u'll get some level of delay.

      - <b>SR-IOV virtualization</b> aware on the devices level [Network interfaces..], now, even in intensive I/O the VM will perform well.        - EC2 enhanced networking.

---

# EC2 Architecture: 
- EC2s are VM (OS+Resources) 
- EC2 instances run on EC2 Hosts. 
  - [Shared Host] or [Dedicated Host], u don't pay for instances on Dedicated. u pay for entire host. 

- AZ Resilience, AZ Fails, Host Fails, Instance fails.
  - can connect to EBS [elastic block store] in the same AZ 
  - No cross sharing to instances in different AZ.

- EC2 [Store, Storage Network, Data Network]:
  - when an instance are provisioned into a specific subnet within a VPC, it's primary elastic network interface is provisioned in that subnet which map to physical hardware on EC2 host.
    - instances can have multiple network interfaces, even in different subnets. but the subnets must be in the same AZ.
  - Storage  :
    - ebs, run inside AZ, can be attached to one instance at a time.

- EC2 instance run on the same host unless, host fails [maintainance/failure..]
  - instance stopped the started again. --> will started in the same AZ but in different host.

- What's EC2 Good for ?
  - traditional OS+Application compute need.
  - Long Running Compute.
  - Monolithic Application Stack.
  - Server Style Applciation.
  - Migirated app workload or Disaster Recovery.
  - either [burst] or [steady-state] load.
- general advice : pick ec2 by default and only move away if u have a specific requirements.

<br>

# TYPES
- considerations
  - ![consideration](images/ec2/considerations.png)
- categories
  - ![categories](images/ec2/categories.png)
  - T -> 
  - C -> compute
  - R -> RAM
  - I -> I/O {}
  - D -> dense Storage
  - P -> parallel processing
  - ![examples](images/ec2/examples.png)

- Decoding EC2 Types:
  - ![types](images/ec2/types.png)
  - Additional Capabilities:
    - may be no letter
    - a -> amd cpu
    - d -> nvme ssd
    ..
- it's cost effective to scale ur app using multiple smaller instances than one large instance.

# EC2 Network & DNS Architecture
- EC2 start with one Elastic Network Interface, u can add secondry one or more.
  - secondary ENI can be detached and attached to another instance.
  - both must be in the same AZ where the EC2 instance is running.
  - ip & dns name appeared if they attached to the ec2 instance but they actually properties for the primary network interface.
    - SGs are on the ENI not on EC2 instance.
  - if u stop&start the instance, the public ip will change.
    - if u restart the instance, the public ip will not change.
  - ![ec2-network-dns-architecture](images/ec2/ec2-network-dns-architecture.png)
    - src/dst checks need to be disabled in case u will use the instance as NAT gateway.
  - ENI has a MAC address and it's visible to the OS.
    - can be used for licensing.
  - exam power up:
    - ![ec2-network-dns-architecture-notes](images/ec2/ec2-network-dns-architecture-notes.png)
 

# AMI
- AMI's can be used to launch EC2 instances.
- AWS or Community Provided.
- Marketplace (can include commercial software) 
  - u pay additional fee for the software.
- Regional..UNIQUE ID e.g. ami-0a313d6098716f372
- Permissions:
  - Your Account
  - Public
  - Specific AWS Accounts
- You can create an AMI from an EC2 instance u want to templatize.
- lifecycle of an AMI:
  - ![lifecycle](images/ec2/ec2-ami-lifecycle.png)
- exam power up:
  - ![exam-power-up](images/ec2/ec2-ami-exam-tips.png)


 <hr>
<br>

# EC2 Purchasing Options (Launch Types)
- On-Demand:
  - u don't receive any capacity reservation. if AWS face a major failure, and capacity is limited, u may not be able to launch the instance. RESERVE Purachase option receive higher priority.
    - not recommended to use for critical components of ur system.
  - ![ec2-on-demand](images/ec2/ec2-on-demand.png)
- Spot
  - u bid for the instance capacity. if the price is higher than ur bid, the instance will be terminated.
  - ![ec2-spot](images/ec2/ec2-spot.png)

# Launch Configurations[LC] & Launch Templates[LT] Recommanded.: 
- AutoScaling group utilize them . 
- perform the same task 
- allow the configurations of ec2 instance to be defined in ADVANCE 
- documents let us configure : AMI, Instance Type, Storage & KeyPair 
  - Networking configuration and sG the instances use. 
  - Userdata & IAM ROLE   
    - Both are not editable 
    - defined once. LT has versions. 
      - LT provide newer features 
      - including T2/T3 Unlimited, Placement Groups, Capacity Reservations, lastice Graphics.

    - LC has one use as part of autoscaling groups. LC provide configurations of ec2 instances.
      - LT can do the same and can use the same configs to launch ec2 instacnes from cli/console.

---

AutoScaling group
used with ELB, LT to deliver elastic architecture - automatic scaling & self-healing for EC2. - Use configs defined in LT to know that to provision. - Has a [MINIMUM], [DESIRED] and [MAXIMUM] size e.g 1:2:4. - Keep running instances at the DESIRED capacity by provisioning
or terminating instances. - SCALING POLICIES automate based on metrics.  
 Scaling Policies: - MANUAL scaling - Manually adjust the desired capacity. - in testing & urgent env. or in more cost control env. - SCHEDULED scaling - Time based adjustment - e.g. Sales.. [known high traffic time] - Dynamic Scaling - SIMPLE - "Cpu above 50%+1", "CPU blow 50-1" <- may need cloudwatch agent. - STEPPED Scaling - Bigger +/- based on instance change in traffic. - from 50%:60% do a specific action from 60:70 another ..etc - take more control than simple scaling. - adjust different load changing - TARGET TRACKING - desired aggregate CPU = 40% .. ASG handle it. [#of req for ELB, IN/OUt NEtwork] - Scaling Based on SQS - ApproximateNumberOfMessagesVisible. - Cooldown Period in seconds.. how long to wait for scaling action before doing the next one.

- Trick [ec2 ASG]

  - if u create a LT which can automatically build an instacne
    then create ASG using this LT. set ASG to use multiple subnets
    in different AZs. the set ASG 1:1:1 then u have a simple instance recovery.
    cheap, simple, effective HA

---

ASG + LBs - ASG can use LB healthchecks rather than EC2 status checks [Applciation AWARENESS]

- Be careful when u setting the HealthCheck of the app. don't depend on DB or static content.
  Scaling Process[functions performed by asg]:

- LANUCH and TERMINATE - vals ->[SUSPEND: asg cannot launch/terminate and RESUME].
- AddToLoadBalancer - add to LB on Launch
- AlarmNotification - accept notification from CW alarms.
- AZRebalance - balances instances evenly across all of the AZs.
- HealthCheck - instance health checks on/off
- ReplaceUnhealthy - Terminate unhealthy and replace.
- ScheduledActions - Scheduled on/off.
- StandBy - use this for instances `Inservice vs StandBy`
  allow to suspend any of activities of ASG on a specific instance, useful
  when u want to perform a maintainance on a specific EC2 instacne [set TO standBY]
  --
  Final Points:
- ASGs are free.
- Only the resources created are billed.
- use cooldown to avoid rapid scaling
- THink about more, smaller instances - granularity.
  - may 2 large instance to scale to 3 will cost more than 20 small instacnes to 21.
- Use with ALB's for elasticity - abstraction.
- ASG defines WHEN and WHERE, LT define WHAT.
  --  
  ASG Scaling Policies:
  ASGs don't need scaling policies - they can have none

---

USer Data:
a script or other configurations run when the instance is launched.

- bootstraping is a process exist outside EC2, general term -> allowing system
  to self configure or perform self configuration steps.
- EC2 Bootstraping
  - Bootstraping allows ec2 build automation.
  - User data - Accessed via the meta-data IP.
  - http://169.254.169.254/latest/user-data
  - anything in USer Data executed by the instance OS.
  - ONLY one time on LAUNCH.
  - EC2 doesn't interpret, the OS needs to understand the User Data.
  - if user data executed incorrectly we now have a Bad configured Instance.
- USer Data Key Points
  - It's OPAQUE to EC2 ..its just a block of data.
  - It's Not secure - don't use it for password or long term credentials.
  - user data is limited to 16KB in size.
  - Can be modified when instance stopped.
  - But only executed once at launch. [post launch configuration].
- Boot-Time-To-Service-Time
  - how long it takes from allocating the instance, and setup configurations.
  - u can bake the bootstraping with AMI to reduce post launch time.
    - after aws provision the instance it will be able to be used immediately.
      /var/log/cloud-init.log /var/log/cloud-init-output.log
      log for execution of user-data.
- EC2 Instance Roles
  - IAM ROLE allows EC2 Service to assume it. but we need to make the app inside the instacne
    to assume that ROLE as well.
  - InstanceProfile : wrapper around IAM ROLE, allow permissions to get inside the instance.
  - when u create an instance role in the console an instanceProfile will be created with the
    same name. but if u create it using cfn/cli u need to create them separately.
  - inside ec2, Temp Credentials delivered instance via meta-data.
    - credentials available inside the meta-data will be always valid. sts & ec2 work together
      to renew it before its expiration.
  - any commands run on CLI tool into the instance will make use of ROLE credentials automatically.

---
# Storage Refresher
  - Direct(lcoal) attached storage - Storage on the EC2 host.
    - called instance store.
      - super fast
      - lost in case:
        - disk fail
        - hw failure
        - migrate ec2 to another host
  - Network attached Storage - Volume delivered over the network (EBS)
    - on-permise env use protocols like NFS, iSCSI, SMB.
    - on ec2 use EBS.
    - highly resilient
    - separate from the EC2 host.
      - can survive issues which impact ec2 host.
  - Ephemeral Storage - Temporary
    - instance store
  - Persistent Storage - Permanent - lives on past the lifetime of the instance.

<br>

- Key terms:
  - Block Storage - u create volume for ex : inside EBS 
    - <b>volume of block storage have #addressable blocks.</b>
      - presented logically as a volume or as blank physical hard drive.
        - no structure byond that.
    - when  u present a unit of block storage to a server, on top of this the Os will create a file system.
      - ex: ext4, ntfs, xfs, zfs, btrfs, apfs, hfs+.
  
    - come in form of spinning disks or SSDs.
      - phsical media : block storage
      - logical volume : backed by different types of  physical storage.
    - Bootable and Mountable.
    - preferred if u want to install an OS or run a database.
  - File Storage - Presented as a file share ... has a structure
    - Mountable
    - Not Bootable
    - preferred if u want to share data between multiple instances.

  - Object Storage
    - collection of objects & metadata, flat.
      - Not bootable, Not mountable.
      - super scalable.
    - preferred if u want to store data for web apps, mobile apps, backups, analytics, etc.

<hr>

# Storage Performance
- IO (block) size - how much data can be read/written at once.
  - ex: 4KB, 8KB, 16KB, 32KB, 64KB, 1MEG.
- IOPS: 
  - Input/Output Operations Per Second.
  - how many IO operations can be performed in a second.
  - ex: 1000 IOPS, 2000 IOPS, 5000 IOPS, 10000 IOPS.
- Throughput : amount of data that can be transferred in a given second MB/s. 
- IO size * IOPS = Throughput.
  - ex: 4KB * 1000 IOPS = 4MB/s.

<hr>

# EBS
- Block storage 
  - raw disk allocations(volume) 
  - can be encrypted using KMS.
  - instances see block device and create file system on this device (exr3/4,xfs)

- Storage is provisioned in <b>ONE AZ</b> (Resilient in that AZ)
- Attachhed to *one{one or more}Ec2 instance or other service over a storage network.
  - multi attached feature allow u to attach the same volume to multiple instances in the same AZ.
    - ex: for clustering.
  - u can detach the volume from one instance and attach it to another.
    - the lifecycle of the volume is independent of the lifecycle of the instance.
      - if the instance moved to another host, the volume will be moved with it.
- snapshot (backup) into s3. Create volume from snapshot (migrate between AZs).
- Different physical storage types, different sizes, different performance characteristics.
  - ex: gp2, io1, st1, sc1, standard.      
- Billed based on GB-month (and in some cases performance)
  - price of 1GB in month = 2 Gb for 15 days = 0.5 GB for 60 days.

- u cannot communicate cross AZs using EBS{EBS in AZ-a cannot be attched to EC2 in AZ-b}.
- EBS replicates within an AZ. Failure of an AZ means failure of a volume.
- Architecture of EBS 
  - ![Architecture of EBS](images/ebs/ebs-arch.png)
----
EBS Volume Types
- gp2
  - default general purpose ssd
  - created with IO created allocation
    - IO is one input/output operation 
    - IO Credit is 16KB chunck of data
      - 1 IOPS is 1 IO in 1 second
  - if u have no credits in this IO bucket u cannot perform any IO on the disk
  - IO 'credit' bucket capacity is 5.4 million credits. Fills at rate of Baseline Performance.
    - 3 IOPS per GB of storage.
      - 3 IO credit per sec per GB.
        - 100GB volume get 300 IO credits per second refilling the bucket.
          - anything blow 33.33 GB get 100 IO credit minimum
          - anything above 33.33 GB get 3 IO credit per second per GB.
          - by default gp2 can burst up to 3000 IOPS.
        - IO credit start full capacity 5.4 million IO credit.
    - every volume has baseline performance base on  its size with a minimum of 100 IOPS.
    - Bucket fills with min 100 IO Credits per second --> regardless of volume size.
      - regardless of any thing else u can consume 100 IO Credits per second which 100 IOPS.
  - ![gp2](images/ebs/gp2.png)
- gp3
  - ![gp3](images/ebs/gp3.png)

# HDD
- ![ebs-hdd](images/ebs/hdd.png)

<hr>

- important for performance questions in the exam:
  - ![ebs-vs-instance-store](images/ebs/ebs-vs-instance-store-1.png)
  - ![ebs-vs-instance-store](images/ebs/ebs-vs-instance-store-2.png)

- EBS snapshots:
  - backup to s3 
    - u protecting the data aganist AZ issues. data become regionally resilient.
    - migrate data between AZs.
    - the first snapshot is a full copy of `data` on the volume.
    - Future snaps are incremental.
      - in incremental u have the risk of losting an incremental backup any next one will not work.
      - EBS is smart enough if u remove an incremental snap it make sure data will move so the next snaps will function. [snap is self-sufficient]
    - volume can be created(restored) from snaps
    - snaps can be copied to another region.
    - ![ebs-snapshot-architecture](images/ebs/ebs-snapshot-architecture.png)
    - ![ebs-snapshot-notes](images/ebs/ebs-snapshot.png)
      - to avoid cost of FSR but with additional admin overhead u can force the restoring by using <br>dd{is a versatile command-line utility that can be used to read and write data at the block level} <br> `dd if=<input_file> of=/dev/null bs=<block_size>` <br> by reading each block so it will be restored immediately.
    - ![ebs-snapshot-exam](images/ebs/ebs-snapshot-consumption-billing.png)


<hr>

# EBS Provisioned IOPS SSD(io1/2)
- IOPS are configurable independent of storage size.
- ![iops](images/ebs/iops.png)

<hr>

# instance store
- Block storage Devices : volume attached to the instance presented to OS and used as basis for file system which can be used by the applciations.
- each ec2 host has its own instance store .
- highest storage performance because it's directly attached to the host.
- included in instance price..
- Attached at launch time only.
- if instance move to another host, the instance store will be lost.
  - stopped and started.
  - changing instance type.
  - hardware failure.

- size and #instance store volumes depends on instance type.
- ![instance-store](images/ebs/instance-store.png)
- ![instance-store-exam](images/ebs/instance-store-exam.png)
---
CloudFormation

- Physical and Logical Resources:

  - begin with a template [JSON|YAML].
  - .. contains logical resources - the "what" [declarative definiation].
    - u focus on what, and cfn deals with how.
  - Templates are used to creae Stacks.
    - .. 1 stack, 100 stacks, 20 .. stacks in each region.
  - template work in any account and any region can be reffered to as portable template.
  - .. stack create [phuysical resources] from the (logical).
  - if a stacks template is changed 
    - physical resources are changed.
  - if a stack is deleted, normally, the physical resources are deleted.

- Resources.Instance.Type
    - .Properties[ImageId, KeyName, InstanceType...]
    - properties are different for each resource type.

  - once logical resource moves to (create_complete) meaning the physical resource is active <br> it can be queried for attributes of the physical resource within the template  [!Ref physical machine id for ec2].
  - cfn aimed to keep the logical and physical resources in sync.
    - ![cfn-logical-physical](images/cfn/cfn-logical-physical.png)
  - any change in the template will cause the stack to be updated.
    - the stack will update the logical resource then the update will reflect on the physical resource.

# Template and Pseudo Parameters

  - (Template Parameters) accept input 
    - console/CLI/API
    - .. when stack is created or updated (ex: size or env[test|dev|prod])
  - (Parameters) defined inside the template. 
    - their values can be refrenced inside the template which allow them to influence the configs of the phyiscal <br> resoures when the template is used to create a stack to provision the resources.
  - can be configured with Defaults, AllowedValues, Min and Max length & AllowedPatterns,
    - NoEcho[make the value when be typed not visible (password)] &
    - Type(String|Int| aws specific which allow u to select vpc from a list or subnet from a list).

  - Parameters.Name.[TYPE, Default, AllowedValue, Description..]
    - ![cfn-template-parameter](images/cfn/cfn-template-params.png)

# Pesudo Prameters injected by aws to the stack.
  - they are exist even you don't define them in the parameter section. injected by aws in the stack.
  - AWS::Region region the stack created in.
  - AWS::StackName
  - AWS::StackId
  - AWS::AccountId
  - populated by AWS based on the environment when creating the stack.

  - Pseudo and Parameters
    - there are useful and ensuring the tempate is portable and can adjust based on  the person or the process that creating the stack.

# Intrinsic Function:

  - allow us to gain access to data at the runtime, ur template can take actions based on how things are when the template is used to create the stack.

  - Ref & Fn::GetAtt

    - reference a value from one logical resource or Parameter on another one.
      - if u create a VPC in a template and u want to make sure another resource such as subnet goes inside that VPC then u can reference the VPC within other logical resources.
    - !Ref return The physical ID of the resource or the value of the parameter.
    - !GetAtt logicalNameOfResource.attributeName
      - return The attribute value.
      - access logical resource attributes.

  - Fn::Join & Fn::Split
    - WordpressURL.Value: !Join ['', ['http://', !GetAtt Instance.DNSName]]

  - Fn:GetAZs & Fn::Select
    - GetAzs is environmental awareness function
      - !GetAZs region 
        - return list of AZs in the explicit region, or the current.
          - az which has a subnet in the default VPC.
          - ![cfn-GetAZs-Select](images/cfn/cfn-GetAZs-Select.png)
    - get a list of az in a given region.
      - select allow us to select element from that list.
        ecample : Instance.Properties.AvailabilityZone: !Select [0, !GetAZs '']

  - Conditions(Fn::IF, And, Equals, Not & Or)
    - create resources based on conditions.

  - Fn::Base64 & Fn::Sub
    - sub allow us to substitue thing within text based on runtime information.
    - ![cfn-Base64-Sub](images/cfn/cfn-Base64-Sub.png)
      - ${Instance.InstanceId} is wrong because it's a self reference.
    - ${parameter} ${LogicalResource} ${LogicalResource.AttributeName}
    - cannot reference itself
  - Fn::Cidr
    - build cidr blocks automatically
    - ![cfn-Cidr](images/cfn/cfn-cidr.png)
    - limitations:
      - based on parent VPC cidr range.
      - cannot allocate or unallocate ranges. 
  - Later .. Fn::ImportValue, FnLLFindInMap, Fn::Transform

<hr>

- Mappings:
  - template can contain a Mapping object.  
    .. which can contain many Mapping.  
    .. which map keys to values, allowing lookup.
  - can have one key, or Top & Second Level.
    - ex: mapping of ami id based on top level key of region and second level key of architecture.
  - Mappings use the !FindMap intrinsic Function
  - Common use ... retrieve AMI for given region & architecture.
  - improve template portability. {exam powerup}
    - let u store some data which can be used to influence how template behave for given input. 
  - ![cfn-mapping](images/cfn/cfn-mapping.png)

<hr>

- outputs{optional}
  - optional, useful for providing status information or showing how to access svcs which created by the cloudformation stack.
  - values can be declared in this section:
    - visible as output when using CLI/console UI
    - accessible from a parent stack when using nesting.{exam}
    - can be exported, allowing cross-stack references.{exam}
  - ![cfn-output](images/cfn/cfn-output.png)
    

- Conditions [optional ]
  - allows the stack to react to a certain conditions, and change infrastructure which is
    deployed or specific configuration for that infrastructure based on those conditions.
  - ..Processed before resoueces are created.
  - Use other intrinsic functions AND, EQUALS, IF, NOT, OR.
  - ..associated with logical resources to control if they are created or not.
  - e.g.. based on parameters: ONEAZ,TWOAZ,THREEAZ - how many AZs to create resource in.
  - PROD,DEV - control the size of instances created within a stack.
- DependOn

  - cfn tries to be effiecient.
    ..does things in parallel (create , update & delete)
    ...tries to determine a dependency order (VPC => subnet => EC2)
    .. (references or functions create these) if a ref b then create b first.
  - DependOn lets u explicitly define these
    ..If Resources B and C denpend on A
    ..both for A to complete before starting.

- Cloudformation provisioning
  - after the physical resource report that it is created -> Create_COMPLETE?
    what if fail in the bootstraping
  - wait conditions & cfn_signal provide a way to get around that limitation
    - Signal
      included in aws-cfn-bootstrap package.
      - configure cfn to hold, wait for 'x' #of success signals.
      - wait for Timeout H:M:S for those signals (12 hrs max).
      - If success signals recieved before the timeout.. CREAE_COMPLETE .
      - If failure signal recieved .. creation fails.
      - If timeout is reached .. creation fails.
      - .. CreationPolicy[EC2, autoscaling group] or WaitCondition[integrated with external ]
      - when needing additional functionality ; pass data back to cfn,
        put general wait state in the template which can be passed until the
        signal is recieved; use [WaitCondition: specific logical resource
        not defined in other resource can depend on other and vs]
        .
        .
        .
        .
- Nested Stacks:
  AWS::CloudFormation::Stack Properties.TemplateURL
  --any parameters without default val should provided in the parent
  whole templates can be reused in other stack.

  - single isolated Stack:
    - Resources in a single stack share the same lifecycle.
    - Stack resource limits (500)
    - can't easily reuse resource e.g. A VPC
      - u can ref resources from the same stack
    -
  - Multi-stack architecture:
    - nested stack - cross stack referencing

  * Root Stack [created first manually]& Parent Stack
    [a parent of any stack it immediately create; any stack has nested one]
  * nestStack Outputs can be referenced in the parent stack nestedName.Outputs.[xxx]
  * nested stack can make dependencies on each other within the same parent.
  * outputs of nestedStack can be injected as parameters to another nested one.
  * Use nested stack when the stacks form part of one solution - lifecycle linked.
    - when resources will not be existed without the other,  
       all created together operate together, deleted together.
  * Overcome the 500 Resource Limit of one Stack
  * Module templates ... code reuse.
  * Make the installation process easier
    - root stack orchestrate the creation of the nested one.
  * USE ONLY WHEN EVERYTHING IS LIFECYCLE LINKED [created, deleted, updated together]
    - if u want to use the actual stack not the template between other stacks -> Cross-stack
  * CFN stacks are designed to be isolated and self-contained [by default cannot reference
    resource in another stack]

- CF Cross-Stack Reference:
  - Outputs are normally not visible from other stacks.
  - Nested stack can reference them...
  - Outputs can be exported .. making them visible from other stacks
  - Exports must have [unique name] in the region
  - Fn::ImportValue can be used instead of Ref.
    that's how to use values from one stack to another.
  - cross stack referencing work when they are in the same region via exported vals.
    ...different lifecycle resources. implement soa. stack reuse.
- StackSet
  - feature of cfn which allow u to create, update, or delete infrastructure across
    many regions in many AWS accounts.
  - deploy cfn across many accounts & regions. rather having to authenticate
    to each account individually and switch to each region u will let cfn to do it.
  - StackSets are containers in an admin account[distinguish where stackset
    is applied from the accounts where resources is created]....
    - contain [stack instances; ref to actual running stack in aws region in account] ..
      which [reference stacks].
  - Stack instances & stack are in 'target accounts'
  - Each stack = 1 region in 1 account.
  - Security = self-managed or service-managed.
  - Term: Concurrent Accounts
    - option can be set when creating a StackSet, define how many individual aws accounts
      can be used at the same time. 2 val for 10 acc u will do 5 set.
  - Term: failure Tolerance
    - amount of individual deployments to fail to mark the whole stackset as failed.
  - Term: Retain Stacks,
    - remove stack instacne from a stackset and by default it will delete
      any of the stacks that are in the target accounts, but you can set it
      so that you can remove stack instances from different AWS accounts and
      different OUs in different regions, and it will retain any of the
      CloudFormation stacks within those regions, within those accounts.
      So by default, it will delete the actual stacks, but you can set it to
      retain them.
  - Scenario: Enable AWS config
  - Scenario: create AWS Config Rules - MFA, EIPS, EBS Encryption.
  - Scenario: Create IAM Roles for cross-account access..
    you might want to use StackSets to create IAM roles that are used
    for cross account access at scale. So instead of having to create
    them in individual accounts one by one, you can define a CloudFormation
    template to create an IAM Role and then deploy it as part of a StackSet.

---

udacity temp:

- why allocation of IP is important ? why not to let the NATG to pick any ?
  - because any aws resource is disposable, if we let NAT choose random IP,
    and for any reason the resource restart it will pick another IP and
    any resources depend on the IP will break. so to make sure that the IP
    will remain the same we need EIP.

Resources:
NatGateway1EIP:
Type: AWS::EC2::EIP
DependsOn: InternetGatewayAttachment
Properties:
Domain: vpc
NatGateway1:
Type: AWS::EC2::NatGateway
Properties:
AllocationId: !GetAtt NatGateway1EIP.AllocationId
SubnetId: !Ref PublicSubnet1

---

Type: AWS::EC2::SubnetRouteTableAssociation
Properties:
RouteTableId: String
SubnetId: String

---

Security groups - Security group specify firewall rules.
We will create two of them, one for a load-balancer and another for a web server.

AutoScaling group - An autoscaling group ensures that a desired
number of servers (EC2 instances) are always up and running. If an instance goes
down due to any reason, such as bad health, a substitute instance with a similar configuration will spin up automatically.

Launch configuration - The configuration of the EC2 instance that
spins up automatically, if required, as a part of autoscaling group resides
in a launch configuration.

Load balancer - A load balancer distributes the incoming traffic
uniformly across multiple servers (target group) within the same or different AZs.
We will also create a listener and target group for the load balancer.

---

Coursera

gui(management) vs cli

- gui is not the best choice when u want to repeat a task[for example: create same resource in different regions]
  aws cli go through python sdk to the api
  vs SDK

all of them use the API.

by default aws credentials stored in ~/.aws/credentials
region and output format[json(default) | yaml | text | ASCII formatted table] stored in ~/.aws/config

A names profile is a collection of settings and credentials that you can apply to a command which enables you
to run its varying sets of configuration variables. It's not uncommon to have multiple types of credentials
you may be dealing with, whether it's working with different environments, different accounts,
or just managing very granular access levels.

aws --region us-east-1 s3 mb s3://coursera_demo_webapp
make bucket

with S3 select, you can just submit a sequel query and S3 will only return
the relevant data. This is both cheaper and faster than downloading the entire
object, when you may only need a subset. All of the processing is on the S3 side,
not on the application side. So you don't have to code for that filtering through
an entire object

AWS Systems Manager Parameter Store. Parameter Store lets you store user-defined
key-value pairs. We're going to use this service to store our S3 bucket name,
whether Dragon data is located, as well as the filename or what we call the key.
This way, if the bucket or the filename ever changes, we won't need to go back
in and change the code. Instead, the parameter can be updated in parameter store.
Then the code will always pull the latest information every time it is run.

To make a request to Amazon Web Services, you first create service client objects for this
specific AWS service you are trying to interact with. The recommended way to do this is to
use the Service Client Builder. Each AGBS service has a service interfaced with methods
for each action on that service API.

default client method on each builder. This method creates a service client with the default
configuration, using what is called the default provider chain to load credentials, as well as
other configurations like what AWS region are you trying to submit your API call to? The default
credential chain, checks a few different places for your AWS credentials. First, it checks the
environment variables. If it can't find the credentials or region, it then checks the Java system
properties. After that, it checks the default credentials profile. If there was not a file configured,
then it checks to see if you are running inside of a container using Amazon Elastic Container Service or
ECS. If so, it will use the credential supplied by ECS. Finally, if it can't find credentials in any of
those places, it will check the instance profile credentials. Essentially seeing if there is a role
associated with the instance that the code is running on, like an EC2 instance, for example.
1- ENV VAR
2- Java System Properties.
3- Default credentials Profile.
4- if u're running inside of a container using Amazon ECS, if so it use credentials supplied by ecs.
5- check instacne profile credentials(IAM ROLE), role associated with the instance code run on.

AWS SDK for Java uses unchecked exceptions:
1- There are two main categories of exceptions to be aware of.
First, you have the AmazonServiceException or one of its subclasses.
2- An AmazonClientException indicates that a problem occurred inside the Java client code,
either while trying to send a request to AWS or while trying to parse a response from AWS.

Temporary Credentials in AWS Cloud9
rotated every 5 mins, can do all actions instead of some of them iam:createuser cloud9:..
to be able to perform action it must be allowed in what temp credentials can do and IAM Role of the user who run the cloud9

u can disable it and configure credentials using -> aws configure
or assume Role that is set to the EC2 instance that run the cloud9

---

- AWS SAM provides a local environment that is similar to the AWS Lambda environment
  to use as a Docker container. You can use this container to build, test and debug
  your serverless applications

- An interface essentially defines how you can interact with a resource. An interface has defined actions,
  defined inputs and defined outputs.
- Interfaces allow you flexibility when it comes to programming. You can define how you want your clients or
  consumers to interact with your service. You define the actions, inputs and outputs.

- Once you're APIs are defined and shared with clients, it's important that you do what you can to maintain
  backwards compatibility with changes.
- API driven development, where the first artifact created out of building a new service is the API. Once the
  API is created, the front end or clients using the API and the actual implementation of the API can be built
  in parallel.

Websocket APIs operate using lower level protocols based on sockets imports and requires the use of IP addresses
and port information. It is bidirectional, stateful, vertically scalable and as ideally used for real time scenarios
such as a chat application is part of a game. HTTP APIs enable you to create restful APIs with lower latency and lower
costs than rest APIs. You can use these to send requests to AWS Lambda functions or to any routable HTTP endpoint.
This could be used when creating an HTTP API that integrates with a Lambda function on the back end. When the client
calls the API, API gateway sends the request to the Lambda function and returns the functions response to the client.

Other features provided by API Gateway are :
1- Canary release deployments for safety, rolling out changes.
2- logging and monitoring through AWS Cloud Trail, and Amazon CloudWatch.
3- Support for custom domain names, throttling of requests:
(API throttling is the process of limiting the number of API requests a user can make in a certain period).
4- direct integration with other AWS services including AWS WAF, AWs X-Ray, and AWS Lambda.

API Gateway provides multiple endpoint types that you can utilize. An API endpoint type refers to the host name of the API.
It can be [edge optimized], [regional] or [private] depending on where the majority of your API traffic originates. - Edge-optimized API endpoints are best for geographically distributed clients. Requests are routed to the nearest
Amazon cloudfront point of presence. This is the default type for API gateway rest APIs. - A regional API endpoint is intended for clients in the same region. When a client running in Amazon EC2 instance
calls an API in the same region or when an API is intended to serve a small number of clients with high demands,
original API endpoint reduces connection overhead. - private API endpoints are a great way to provide a client secure access to resources inside of an Amazon virtual private cloud.
Private APIs are isolated from the public Internet, and they're only accessed using VPC endpoints for API Gateway that have
been granted access

    1- an HTTP API. This is designed to offer REST-based HTTP APIs at low latency and low cost. HTTP APIs
        are used to proxy back-end resources and are supposed to be simple and fast. This is great for when
        you want API gateway to simply taken in request, authorize it and pass it on to the back-end resource,
         like a lambda function or an HTTP end-point.

    2- WebSockets, unlike HTTP, is a stateful communications' protocol. WebSocket APIs allow for support for
       applications that need real-time data or real-time communication.

    3- REST APIs with API Gateway uses HTTPS and is stateless. This is very similar to HTTP APIs, but it offers
       some different functionality. REST APIs allow you to have full control over the response and requests between
        your client and API gateway. You can apply what are called models and mappings to validate and transform
        requests and responses.

    4- a Private REST API. This is the same thing as a normal REST API, but it allows to set up an API that can
       only be accessed from a VPC, creating a private API. This is useful for internal APIs that you do not want
       to expose to outside clients.

    request hit:
      [method request], which is the first step for API gateway to accept a request. This is where you can apply
       authorization and data payload validation
      Next, it is passed to the [integration request]. This is where you configure what back-end service you are
       fronting with API gateway, as well as applying any data transformations that you may require.
      [backend svc]
      An [integration response] is an HTTP response encapsulating the back-end response. You can configure how
       your back-end service responses map to HTTP responses and apply data transformations at this step as well.
      Method responses are similar to method requests. They are responsible for validating and fitting responses to models.
       The only real difference is that they're applying this data validation to the response not to the client.

    API Gateway REST APIs provide a way to validate incoming requests against models and data can be transformed
     in shape by using API Gateway mappings. API gateway supports multiple types of APIs, like HTTP APIs and web sockets.

    using API Gateway as just a proxy for your back-end, and you won't need to do any data validation or transformation at all.
     You could easily use API Gateway, HTTP APIs for that use case.

    Models can be applied to both method requests and method responses. You have API Gateway checking the structure
      of the data on the way in and on the way out. It's also important to note that each method for resource like,
       get, post, etc, could have different models. So these get applied at the method level, not at the resource level.

    Mappings are applied to the integration request and integration response of your API. They're used for REST HTTP
     integrations, not proxy integrations, which just take the data as is and passes it to the back-end service without
     transformation. These mappings, like models, are also applied at the method level, and each method under a resource
      can have its own mappings.

      Mappings are written in Velocity Template Language or VTL.

      If you already have defined a model for the method, API Gateway can generate a VTL blueprint for the mapping,
       which you can then modify. A mapping template assumes the data coming in as a JSON object by default.

      Mapping support conditional statements, can inject new parameters into the payload, can hard-code values, which
       is needed for mocking, map data in complex structures, and can even reference data made available at run-time,
       such as context and stage variables

     what is this dollar input? API gateway provides variables that start with a dollar sign that give you access to payload,
     and context information in your mappings. In this case the dollar input variable is giving the mapping access to the
     request payload and parameters. - similar to this dollar input, like dollar context, dollar util, and dollar stage variable.

     - API Gateway is validating the incoming request against the model.

---

serverless:

- a cloud computing execution model in which the cloud provider allocates machine Resources
  on demand. taking care of the servers on behalf of their customers.
- No more servers u think about.

---

EC2: still requires management and maintenance by its users, like
operating system updates or security patching.

Good Reasons for Choosing EC2 over Serverless
1-Computation- workloads that require a high level of (parallel) computing power.

2-Flexibility & Control- With EC2, you have complete control over the operating system, libraries,
and every software that runs on your instance.

3- Cost Control , you’ll be billed for your chosen instances that can be a perfect fit
for your needs in regard to instance types and sizes.
--
Amazon Machine Images (AMI)
AWS-maintained configurations that are required to launch an instance. It contains: - OS - Architecture - launch permissions - storage

Elastic Block Storage (EBS):

- block storage volumes that can be mounted to instances. Their persistence is i
  ndependent of your instance’s lifetime.

Elastic File System (EFS) - scalable file storage that can scale based on workload requirements.
Use it for any workload that can suddenly increase or decrease storage needs.
It can also be used for shared volumes.

depending on the operating system that is running on your EC2 instance, you may have to use another user name.
For example, the user name for Amazon Linux instances is ec2-user while the user name for Ubuntu instances is ubuntu.

you won’t be billed for the instance while it’s stopped, but you’ll still pay for the storage of any EBS volume.

Hibernate: If supported by the OS, the instance can save its RAM contents to the EBS root volume.

security Groups - A security group can be viewed as a virtual, stateful firewall for your instances.
It controls the traffic to and from each instance, allowing you to specify which inbound and outbound
network traffic is allowed.

Network ACLs - NACLs are another layer of security that allows you to control traffic to and from subnets
in your VPC. Contrary to security groups, NACLs are stateless, meaning that you explicitly need to configure
outbound allow rules, for your service to be able to answer requests. The stateful security groups allow such
responses by default, if the requesting (inbound) direction was allowed.

Gateways - There are two important gateway types in AWS: the Internet and the NAT gateway. On the one hand,
the internet gateway allows communication between instances in your public subnet and the internet.
A NAT gateway on the other hand enables instances in a private subnet to connect to the internet or other
AWS services but prevents the Internet from initiating connections to those instances.

EC2 is the key service for applications that require a high level of customization and control,
while serverless services like Lambda are a good choice for applications that need to run code
in response to specific events or triggers.

--AuthN & AuthZ--------------
For the REST API type, the mechanisms for API Gateway to do authentication and authorization,
our identity and access management, AWS Authorizers, and Amazon Cognito user pools. There are a few other mechanisms related to access control
around this type of API. Those are the resource policies applied at an API gateway endpoint, endpoint policies for interface VPC endpoint,
Cross Origin Resource Sharing or CORS, AWS WAF, a Web Application Firewall, Client-side SSL certificate, and usage plans with API keys.

For HTTP API's, the only way to do authentication and authorization is by using JSON Web tokens or JWT that are part of the openID Connect an OAuth2.0 protocols. and aws Cognito user pool.

---

With a growing application and therefore a growing number of containers, you’ll be facing operating challenges regarding
the management and orchestration of your container landscape: deployments, scheduling, and scaling of your
containers, and many more. [[ECS]] was built to help with that tedious task by offering a management plane that simplifies
all those tasks so that developers can focus more on application development.

Task, Task Definition, service:

- Task Definition: The Blueprint to Run Your Containers
  - Launch type - which service do you want to use to execute your tasks, either EC2,
    Fargate, or External?
  - Roles - you’ll need to have two dedicated roles for ECS. [permissions to pull image, read secrets, svcs u want to access]
  - Container image
  - CPU & memory allocation
  - Environment variables -
  - Secrets - you can securely inject sensitive data from AWS Secrets Manager
    or Systems Manager Parameter Store.
  - Logging configuration - define the log driver you want to use and the destination
    where ECS should send your logs. The available log drivers are also dependent
    on your launch type, e.g. Fargate only supports awslogs, splunk, and awsfirelens
    while EC2 additionally offers fluentd or json-file.
  - Exposed ports
- Task - A Containerized Application That Is Deployed to Run on EC2 or Fargate
  - an actual execution of a task definition. They consist of a set of containers that
    are run together on the same host.
  - Tasks are defined using the Docker Compose file format
- Service - Managing a Group of Tasks
  - make sure a desired #of running tasks is also running.
- A cluster is a logical grouping of tasks or services. Tasks and services run
  on infrastructure that is registered to a cluster, either provided by AWS Fargate,
  EC2 instances managed by yourself or on-premise servers, or virtual machines that
  are remotely managed.
- Launch Types - The Way in Which Tasks Are Run and Managed[EC2, Fargate, External]
  EC2: - It’s a good launch type choice for workloads that require consistently high CPU and
  memory, workloads that are optimized for pricing, or applications with persistent
  storage requirements
  Fargate: Run Tasks without Having to Manage the Underlying Infrastructure - It’s the recommended launch type for tiny to large workloads that may require
  low overhead and experience occasional bursts. If you’re not perfectly certain
  about your full requirements, always go for the Fargate launch type to minimize
  operations and liabilities.
  ECS provides different scheduling capabilities, including a service
  scheduler or manually running tasks

The lifecycle of a task: - Provisioning - Preconditions are in progress, e.g. attaching
the Elastic Network Interface (ENI) if the task resides in a VPC. - Pending - Waiting until the required resources for the task are available - Activating - Final steps that takes place before moving to the running state,
e.g. if attached to load balancing, ECS takes care of registering the task to the target groups - Running - The task is successfully running. - Deactivating - Necessary steps to take place before stopping the task, e.g. detaching
from target groups if the task is part of a load balancer. - Stopping - Gracefully shutting down the containers via SIGTERM signals. If a container does
not stop within the configured timeout, there will be a forceful shutdown via SIGKILL. - Deprovisioning - Final steps before transitioning to the stopped state, e.g.
detaching the ENI if the task resides in a VPC. - Stopped - The task has been successfully stopped.

event, concurrency and trigger. An event is a JSON-formatted document that contains data for a function to process.
The Lambda runtime converts the event to an object and passes it to your function code. When you invoke a function,
you determine the structure and contents of this event.

if you wanted to use a different language like PHP, for example, you will want to look at an AWS Lambda custom Runtime.
This type of Runtime, you will need to build it yourself. A Runtime is responsible for running the function setup code
and reading the input data of the event from the Lambda runtime API. The runtime passes these events that it receive to
the functions main that we call a Handler. Then it posts the response from the Handler back to Lambda, the service,
so it can be sent back to the client.

You can specify how much RAM you want available to your Lambda function code from 128 megabytes all the way to three gigabytes
and 64 megabyte increments. As for the CPU, it's not as setting you control directly, instead, Lambda allocates the CPU power
linearly in proportion to the amount of memory that is configured. For example, at 1,792 megabytes, a function has the equivalent
of one full virtual CPU.

It's an important setting to be aware as there are limits in each AWS account for how many Lambda functions can run
concurrently per region. This is a setting that you can request to be raised and it starts at 1,000.

AWS commonly relies on role based access, where you create a role in IAM that has a policy associated with it.
That allows or denies certain AWS API calls to be made. AWS services, applications or identities can then assume
the role and gain access to temporary credentials. Which are supplied by AWS security token service and contain
the permissions defined in the policy associated with the role.

an execution permission is the role that allows whatever API calls your Lambda function needs to make.
Once the Lambda is running, it will assume the role and then it has its executions permissions.

even if your code isn't reaching out to any AWS services at all. You still probably want the basic
execution rule, so we can post logs and metrics for monitoring and debugging.

Lambda functions need basic execution permissions as well in order to do things like posting logs to Cloud watsh
logs or sending tracing data to AWS X-Ray. AWS has a managed policy used for this called AWSLambdaBasicExecutionRole.

A trigger is generally another AWS service account or AWS entity that invokes your Lambda function under certain event conditions.

Each Lambda function has its own resource based policy and it uses the same IAM policy language, roles, and other permissions in AWS use.
You can allow or deny certain entities from interacting with your Lambda function.

The trace allows you to control how you want to acquire a representative sample of the request, that your application serves,
through a sampling algorithm to determine which requests get traced. The default sampling rate is fairly conservative,
only recording the first request each second, and 5% of any additional requests. While this ensures that you're getting
at least one sample per second, you also have the ability to configure X-Ray to modify the default sampling rule,
and configure additional rules that apply sampling based on properties of the service or request.
