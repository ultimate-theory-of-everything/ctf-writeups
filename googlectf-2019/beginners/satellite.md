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
Username: brewtoot 
password: ********************
166.00 IS-19 2019/05/09 00:00:00	
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

**Flag: CTF{4efcc72090af28fd33a2118985541f92e793477f}**

There's another way to retrieve the flag, not sure if that's intended.

Let's run `file` command on the executable:

```
> file init_sat
init_sat: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, Go BuildID=YhfyV09rKV_0ewkLiNr1/6ZJO5J8awFQSRgZDzlnA/zvyuoO7Qu3ralSU_Aheb/QK0rATh0jzljJY8j2313, not stripped
```

It's unstripped 64-bit ELF. Let's try to get strings:

```
> strings init_sat > strings.txt
```

The output seems to contain all the strings in the universe, but among them there are two important ones:
`CTF{\S{40}}`, which makes us realise that the password mask is probably being applied on the client side, and `satellite.ctfcompetition.com:1337` which is the name and the port of the remote machine the client connects to.
Let's netcat it:

```
> netcat satellite.ctfcompetition.com 1337
Welcome. Enter (a) to display config data, (b) to erase all data or (c) to disconnect
> a
Username: brewtoot 
password: CTF{4efcc72090af28fd33a2118985541f92e793477f}
[...]
```
