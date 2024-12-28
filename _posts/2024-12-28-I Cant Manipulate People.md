---
title: "I Cant Manipulate People"
categories: [CTF, Wargame2024]
tags: [forensic]
date: 2024-12-28 21:13:00 +0100
media_subpath: /assets/img/posts/wargame2024/
---
## Challange
Partial traffic packet captured from hacked machine, can you analyze the provided pcap file to extract the message from the packet perhaps by reading the packet data?

## Solution 
I opened the `PCAP` file using Wireshark.

![](WarGame2024WireShark.png)

After reviewing the files, I found several `DNS`, `HTTP`, and `ICMP` messages.

I initially focused on the packages that were not `ICMP`, as most of the messages were `ICMP`. I didn’t find anything noteworthy in that category. Then, I decided to examine the `ICMP` messages.

![](WarGame2024WireSharkICMP.png)

The most interesting part was that all the ICMP packets contained two hex characters in the data portion. I manually converted the first three hex values to ASCII and obtained the string `WGM`, which suspiciously resembles the beginning of a flag.

## Automation
However, I don't want to convert each one manually, so I extracted the hex data using the following command:
```shell
tshark -r traffic.pcap -Y "icmp" -T fields -e data > hexoutput.hex
```

which got me the following hex outputs: 
```
57
47
4d
59
7b
31
65
33
62
37
31
64
35
37
65
34
36
36
61
62
37
31
62
34
33
63
32
36
34
31
61
34
62
33
34
66
34
7d
```

Using this file, I created a Python script to convert it into an output.
```python
values =[]

with open("CTF/Wargames 2024/I cant manipulate people/hexoutput.hex") as file:
	values = file.readlines()

for i in values:
	print(bytearray.fromhex(i).decode(), end="")
```

Which got me the flag:  `WGMY{1e3b71d57e466ab71b43c2641a4b34f4}`
