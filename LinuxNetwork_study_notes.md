---
title: Linux Networking course notes
Created: 2016/07/02
Last modified: 2019/07/02 17:31:16
---


Network Topology:

	- Switches:	Forwards packets within the internal network
	- Routers:	Forwards packets between networks

	Data travels through both to reach a destination outside of the Lan
	Cloud networking is a software defnied simulation of a traditional network

	Communication flow:
	!PC! --> via eth0 -->  !Switch! --> via default route to the gateway --> !Router! (which is the gateway) --> |Internet| 

OSI Layers (Open Systems Interconnection model):

	7: Application
		- Description:  The <interface> permitting the user to send and receive data through clients and applications.
		- Conceptual: Individual using a telephone to make a call to order something. The phone is the <interface>.
		- Actual: A user clicking on a link within a web browser. The browser application provides the <interface>
		- Example: <HTTP>, POP3, SMTP, DNS, SOAP
		- Troubleshooting: Verify if application is functional and DNS
	6: Presentation
		- Description: Converts the request from the application into a universal format.
		- Conceptual: The Individual using the telephone needs to speak a language the other person understands.
		- Actual: The web browser renders the page by <converting> the files stored on the remote server into formats that permit displaying the content.
		- Example: <TLS>, SSL, MIME, JPEG, GIF
		- Troubleshooting: Rarely issues at this layer if application is performing normally.
	5: Session
		- Description: Permits the ability to open, close, and manage a session between the process and response.
		- Conceptual: When our user dialed the telephone, someone on the other end needed to pick up so the conversation could begin.
		- Actual: When a user clicks a link in a web browser, the browser will initiate a TCP connection to the web server. The web server will send a response and close the connection. The Browser will <manage> the additional TCP connections necessary to load assets externally referenced by the web server.
		- Example: <SSL>, LDAP, NetBIOS, PPTP, RPC, SMB  #SSL is technically a level 5 layer, but it's initiated at level 6, and then processed at level 5. 
		- Troubleshooting: Rarely issues at this layer if application is performing normally.
	4: Transport
		- Description: Defines how data will be sent, providing validation and security.
		- Conceptual: Our user has placed the order and is waiting on delivery.
		- Actual: The web browser breaks up the TCP requests into manageable portions, applies a label to protect the ordering, and transports the pieces across the sessions.
		- Example: <TCP>, UDP, SPX, iSCSI
		- Troubleshooting: Verify blocked (or damaged) ports, firewalls, and QoS
	3: Network
		- Description: Looks for the best <path> to reach the destination. <think IP addresses>.
		- Conceptual: The order has been fullfilled and shipped. The shipping provider uses GPS to determine the most direct and efficient <route>.
		- Actual: The web browser compuAter uses the IP address to determine if the web page is local or remote, and how to reach the remote IP, via the default gateway. The computer creates a message (packet) and addresses it to the web server with it's own return address.
		- Example: <IP>, IPSec, ICMP, RIP
		- Troubleshooting: Verify addressing and routing configuration, bandwith, and authentication
	2: Data Link
		- Description: Handles communication between <adjacent nodes>. <Think MAC addresses>
		- Conceptual: The shipping company needs to know the full deliver address.
		- Actual: The packets are structured into messages called frames, to be sent to the default gateway. These frames contain the source and destination IP addresses, as well as the source and local destination MAC addresses.
		- Example: <Ethernet>, Frame Relay, IEEE 802.11 (WiFi), PPP, VLAN, MAC
		- Troubleshooting: Verify switch and VLAN configurations as well as MAC addressing and IP conflicts.
	1: Physical
		- Description: Handles bit level transmission between network nodes.
		- Conceptual: The shipping company's truck navigates a network of roads and interstates to deliver the order to the delivery address.
		- Actual: This layer is hardware specific, and is the actual physical connection between the computer and the network. 
		- Example: <Ethernet physical>, IEEE 1394 (firewire), infrared
		- Troubleshooting: Verify physical components

Anatomy of a IP Address:



//Useful commands
ip addr show # replaced ifconfig
ip route show # route table
routel # route table
