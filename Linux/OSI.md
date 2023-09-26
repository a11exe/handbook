# OSI Model

[#linux], [#interview]

The modern Internet is not based on OSI, but on the simpler TCP/IP model. 
However, the OSI 7-layer model is still widely used, 
as it helps visualize and communicate how networks operate, and helps isolate and troubleshoot networking problems.

![osi](OSI-7-layers.jpg "OSI")

# OSI vs TCP / IP Model

![tcp](OSI-vs.-TCPIP-models.jpg "TCP")

The Transfer Control Protocol/Internet Protocol (TCP/IP) is older than the OSI model and was created by the US Department of Defense (DoD). A key difference between the models is that TCP/IP is simpler, collapsing several OSI layers into one:

OSI layers 5, 6, 7 are combined into one Application Layer in TCP/IP
OSI layers 1, 2 are combined into one Network Access Layer in TCP/IP – however TCP/IP does not take responsibility for sequencing and acknowledgement functions, leaving these to the underlying transport layer.
Other important differences:

TCP/IP is a functional model designed to solve specific communication problems, and which is based on specific, standard protocols. OSI is a generic, protocol-independent model intended to describe all forms of network communication.
In TCP/IP, most applications use all the layers, while in OSI simple applications do not use all seven layers. Only layers 1, 2 and 3 are mandatory to enable any data communication.

Based on [osi model](https://www.imperva.com/learn/application-security/osi-model/)