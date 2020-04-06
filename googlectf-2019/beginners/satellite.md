# Satellite

**Given**: archive with two files - init_sat binary and README.pdf

Running init_sat gives output:
```
Hello Operator. Ready to connect to a satellite?
Enter the name of the satellite to connect to or 'exit' to quit
```
Typing 'osmium' (hint on the picuture in README)
```
Establishing secure connection to osmium satellite...
Welcome. Enter (a) to display config data, (b) to erase all data or (c) to disconnect
```
Option (c) disconnects, as expected, option (b) gives `Unsufficient privileges` message. Trying option (a):

```
Username: brewtoot password: ********************	166.00 IS-19 2019/05/09 00:00:00	
Swath 640km	
Revisit capacity twice daily, anywhere 
Resolution panchromatic: 30cm multispectral: 1.2m	
Daily acquisition capacity: 220,000km²	
Remaining config data written to: https://docs.google.com/document/d/14eYPluD_pi3824GAFanS29tWdTcKxP_XUxx7e303-3E
```
`Remaining config data` links to a google doc with a base64 string in it:
```
VXNlcm5hbWU6IHdpcmVzaGFyay1yb2NrcwpQYXNzd29yZDogc3RhcnQtc25pZmZpbmchCg==
```
Decoding base64 with a python snippet:
```
>>> import base64
>>> message='VXNlcm5hbWU6IHdpcmVzaGFyay1yb2NrcwpQYXNzd29yZDogc3RhcnQtc25pZmZpbmchCg=='
>>> base.base64decode(message)
b'Username: wireshark-rocks\nPassword: start-sniffing!\n'

``` 
Immediately upon the connection to 'osmium' whireshark captures a DNS request to resolve `satellite.ctfcompetition.com`, in the response we see it being resolved to 34.76.101.29.

Filtering out packets by the source ip = 34.76.101.29 and chosing option (a) in init_sat cli we get the first flag in the response TCP packet:

**Flag #1: CTF{4efcc72090af28fd33a2118985541f92e793477f}**
