# 01 – Wireshark Basics

## Introduction

In order to begin capturing ICMP packets and to be able to address 
the information to be learned without any issues, it is necessary to 
understand what information we expect from Wireshark.

This will not be a guide or breakdown of the Wireshark program or the 
individual tabs of the interface. In this chapter, I offer the reader — 
whether experienced or a beginner — my perspective on the first encounter 
with this program.

What information do I consider important?  
What kind of information do I expect?  
What can be derived from this information?

Since there are already plenty of tutorials and explanatory videos about 
this program available on the web, I do not wish to add to them, but rather 
to simply and soberly build a basic understanding step by step through 
practice, exercises, and above all curiosity — and to further develop and 
expand upon it with each subsequent project.


## What is Wireshark?

Wireshark is a free, open-source network analysis tool originally developed 
by **Gerald Combs** in **1997** in the **United States**. It was initially 
released under the name **Ethereal** and later renamed to **Wireshark in 2006**.

It is — to my knowledge — primarily used in the field of cybersecurity 
and network administration.

Its core purpose is to show the user which information is entering and 
leaving the network the computer is connected to — including **who** sent it, 
**what** the content is, **when** it arrived, and much more.
This is achieved through so-called **packet capturing** — Wireshark intercepts 
and displays the raw data packets traveling through the network in real time.

This makes it an extremely useful tool for:
- Detecting cyberattacks early and defending against them
- Investigating successful attacks after the fact
- Preventing future attacks
- Protecting your own machine
- Helping both companies and individuals identify weak network structures 
  or active threats
- **Testing network topologies and infrastructures** — Wireshark allows you 
  to verify whether a network is built and functioning correctly
- **Diagnosing network issues** — it helps identify why and how packets 
  disappear (packet loss), take too long to arrive (high latency/delay), 
  or behave unexpectedly within a network

> ⚠️ **Note:** Wireshark is in itself a complete topic that could easily 
> fill an entire project on its own. I am deliberately keeping this section 
> brief — I am aware that I am leaving out a lot of information, but I am 
> doing so intentionally in order to stay focused on the actual core of 
> this project: **ICMP packet analysis.**

## Starting Wireshark

Since I am running this project on Kali Linux, I will note and break down every necessary terminal command.

To launch Wireshark, we open our terminal (Ctrl+Alt+T) and enter:
sudo wireshark, where "sudo" stands for "superuser do" and thus acts as the master key to execute any command on this machine (Note: The host's password will be prompted).


![alt text](image-1.png)


After successfully entering your password, the following start page of the Wireshark program opens. As expected, a large number of tabs, information, and configurations are immediately visible at first glance.


![alt text](image-2.png)


In order not to get lost in this forest of information, I have highlighted the filter tab — which, even before launching the program, seemed to me the obvious first thing to look for, since I only want to work out a specific type of information for myself here: ICMP.


![alt text](image-4.png)


We already get a sense that our input is valid, as the input field turns green after we have entered the protocol we exclusively want to capture. This saves us an enormous amount of stress and sorting effort.

You will get an idea of what I mean if you have ever opened Wireshark while nothing else was active on the desktop — except for an internet connection!
I personally find such background noise as a beginner to be very overwhelming, as you keep discovering more and more knowledge gaps and can quickly get lost in the sheer volume of data. For this reason, I have deliberately chosen for this project which information to include and which to leave out — since during my research I often had to set things aside the moment I noticed I was drifting too far from the main topic. With that I want to make it clear that I am not ignoring or hiding any information here, but rather taking everything into account and either incorporating it into the project, or categorizing it and setting it aside for future projects.

With that, our settings for capturing our packet are ideally configured and Wireshark now knows what we want displayed and what we don't. By clicking on the red arrow button, Wireshark begins capturing packets.



Once the packet capture begins, we are immediately brought to an empty window. This is where the first advantage of our pre-set filter becomes apparent — as long as we do not send (or receive) an ICMP request, these windows remain empty, allowing us to calmly prepare for the information that awaits us — information that, upon arrival, will directly overwhelm us if we do not know what to expect and where.

For this reason, I have divided this screen into 3 areas for easier explanation, which we must go through in detail before we can even send and receive our first packet!


![alt text](image-5.png)


Area 1 is divided into 7 columns, which can be broken down quickly:

![alt text](image-6.png)



1. **No.** — Displays the frame number (e.g. *Frame 1, Frame 2, ...*), representing the order in which the packets arrived during the capture session.

2. **Time** — By default, this value shows the elapsed time in seconds since the capture was started. It can optionally be changed to Unix Epoch Time (seconds counted since January 1st, 1970) via *View → Time Display Format → Unix Epoch*.

3. **Source** — The source IP address, meaning the IP address of the sender of the packet.

4. **Destination** — The destination IP address of the packet, indicating where it is being sent to.

5. **Protocol** — The protocol in which the packet was composed (e.g. ICMP, TCP, UDP).

6. **Length** — The total length of the packet, measured in bytes.

7. **Info** — A brief summary of the packet's content, providing a quick overview of what the packet contains without having to inspect it in detail.


------------------------------------------------------------------------------------------------------------------------------


Area 2 and 3 are more closely connected when it comes to gathering information. While Area 3 displays the packet as hexadecimal code, Area 2 allows us to understand how that code is structured and what information can be read from it.


![alt text](image-7.png)


In Area 2 we can expect four tabs: 

the "Frame" tab, which tells us the number of the packet since the start of the capture. This makes it easier to find and match related packets such as a Request and its corresponding Reply. This tab also highlights the complete hexadecimal code in Area 3.

The next tab is "Ethernet II", which covers the first 14 bytes and contains information about the sender, the receiver, and the protocol type that follows this header.

The "Internet Protocol (IP)" tab covers the next 20 bytes (15–34) and contains information about how the packet is transmitted across the network.

Finally, the "ICMP" tab covers the last 8 bytes (35–42) and contains the actual request information of the packet, including type, code, checksum, identifier and sequence number.


------------------------------------------------------------------------------------------------------------------------------


Now we are prepared enough to fill these empty fields with new information and go through them. So let's send a command to capture both a **request packet** and a **reply packet** for a better overview.

To reliably measure connection speed, a certain number of request packets is usually sent (**Linux: infinite until manually interrupted, Windows: 4 packets** by default). However, this is not necessary for our packet analysis, since we are less interested in the *content* of the packet and more in its **structure**.

We also need to provide the command with a **destination IP address or URL**.

Here is what the command looks like on Linux:

```bash
ping -c 1 8.8.8.8
```

| Part | Meaning |
|------|---------|
| `ping` | The command to send a request packet |
| `-c 1` | Count 1 – how many packets should be sent |
| `8.8.8.8` | Our target IP – Google's DNS server, commonly used to test connectivity as it is reliably reachable |

> 💡 **Note:** `8.8.8.8` is Google's **public DNS server**. An alternative is `1.1.1.1` by Cloudflare, which is also commonly used for connectivity tests.



------------------------------------------------------------------------------------------------------------------------------




![alt text](image-10.png)


We enter the command in the Linux terminal and after pressing enter, it won't take long — within seconds, **2 packets** should appear and fill our **areas in Wireshark**.


Even without Wireshark, the terminal already gives us some useful information after running the ping command:

```
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=40.7 ms
```

| Field | Value | Meaning |
|-------|-------|---------|
| `56(84) bytes` | 56 / 84 bytes | The size of the data being sent – 56 bytes of payload, 84 bytes total including the ICMP header |
| `icmp_seq=1` | 1 | ICMP Sequence Number – this is the 1st packet sent. If multiple packets are sent, this number increases (1, 2, 3...). It helps identify lost or out-of-order packets |
| `ttl=118` | 118 | Time to Live – every packet starts with a TTL value and loses 1 for each router (hop) it passes through. When TTL reaches 0, the packet is discarded. Windows starts at **128**, Linux starts at **64**. Since we received a TTL of 118, Google's server likely started at **128**, meaning the packet passed through **10 hops** to reach us |
| `time=40.7 ms` | 40.7 ms | The round-trip time – how long it took for the packet to travel from our machine to 8.8.8.8 and back |

---

```
--- 8.8.8.8 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 40.668/40.668/40.668/0.000 ms
```

| Field | Meaning |
|-------|---------|
| `1 packets transmitted, 1 received` | We sent 1 packet and received 1 reply – no packet was lost |
| `0% packet loss` | The connection is stable – all packets arrived successfully |
| `rtt min/avg/max/mdev` | Round-trip time statistics: minimum / average / maximum / deviation. Since we only sent 1 packet, all values are identical at **40.668 ms** |

> 💡 **Note:** Make sure Wireshark is already **capturing on the correct interface** before running the ping command, otherwise the packets might not be recorded.

------------------------------------------------------------------------------------------------------------------------------


![alt text](image-8.png)


We can now see our 3 familiar areas again — this time filled with data.

In **Area 1**, after our preparation, only the **Info column** should still raise some questions, which I'd like to address directly.

![alt text](image-12.png)

---

### Breaking Down the Info Column

At the very beginning, the **ICMP type** is declared:
- **Request** – I am asking / sending a packet out
- **Reply** – The answer comes back

---

| Field | Meaning |
|-------|---------|
| `id=0x3bf5` | The **ICMP Identifier** — used to match a Request with its corresponding Reply. When multiple ping processes run simultaneously, the ID helps distinguish which reply belongs to which request. `0x3bf5` is the hexadecimal representation of **15349** in decimal — this value is randomly assigned by the operating system for each new ping session |
| `seq=1/256` | **1** = our packet sequence number (the 1st packet sent). **256** = the raw **Big-Endian** representation of 1 in a 16-bit format. This pattern continues: 2/512, 3/768, etc. I will go deeper into this type of number representation once we break down the packet details — it is also **relevant for attack detection**, as manipulated or unexpected sequence values can indicate **ICMP-based attacks such as ping floods or crafted packets** |
| `ttl=64` | **Time to Live** of our outgoing Request packet — since our machine runs Linux, it starts at **64** (as discussed earlier in the terminal output) |
| `ttl=118` | **Time to Live** of the incoming Reply from 8.8.8.8 — Google's server started at **128**, and the packet passed through **10 hops** to reach us |
| `(reply in 19)` | A Wireshark reference — the corresponding **Reply packet** can be found in **row 19** of the packet list |
| `(request in 17)` | A Wireshark reference — the corresponding **Request packet** can be found in **row 17** of the packet list |

> 💡 **Note:** These Wireshark row references are extremely useful when analyzing traffic — they allow you to instantly jump between a request and its reply, making it easy to verify response times and confirm that no packets were lost.

------------------------------------------------------------------------------------------------------------------------------

After breaking down Area 1 in detail, the next interesting source of information is hidden in the **interaction between Area 2 and Area 3**.


![alt text](image-14.png)


---

### Area 3 — The Raw Packet

Area 3 caught my personal interest the most — here we are one step closer to the raw message, almost exactly as the device itself receives it. It is displayed in **hexadecimal**, though the actual transmission happens in **binary**.

In my opinion, professional and effective troubleshooting goes far beyond simply reading error codes. Complex issues demand the **deepest possible understanding of the underlying subject** — in this case, data packets and transmission protocols. Only with this level of knowledge can the true **root cause** of an error or an attack be reliably identified and fully resolved, rather than just partially patched.

> 💡 **Why hex and not binary?**
> Binary data is extremely long and hard to read for humans. Hexadecimal is a compact representation — every **1 byte** (8 bits) can be expressed as exactly **2 hex characters**, making it much easier to read and analyze while still being close to the raw data.

---

### Area 2 — The Translator

Area 2 acts as a **translator** between the human-readable labels and the raw hex data.

When you **hover over any field** in Area 2 — such as one of the headers we already discussed — the corresponding bytes in the hex code on the right side are **highlighted**, showing you exactly which part of the raw packet contains that specific information.

In the screenshot, I am hovering over **Frame 17** — and as we can already derive from our preparation, the **entire hex code is highlighted**, since the Frame layer represents the complete packet from start to finish.

> 💡 **Note:** This highlight feature is one of Wireshark's most powerful tools for learning and analysis — it creates a direct visual link between a protocol field and its raw bytes, making it possible to understand exactly how data is structured at the byte level.

------------------------------------------------------------------------------------------------------------------------------

![alt text](image-15.png)


To not leave the elephant in the room unaddressed, we need to briefly talk about the ASCII representation displayed alongside the hex code in Area 3.

ASCII — The Human-Readable Side
When Wireshark displays the raw packet data, it shows two columns side by side:

Column	What it shows
Hex	The raw bytes in hexadecimal — precise and complete
ASCII	A human-readable interpretation of those same bytes — where possible
ASCII (American Standard Code for Information Interchange) is a character encoding standard that maps numbers to characters. For example, the hex value 0x41 represents the letter A, 0x48 represents H, and so on.

However — in an ICMP packet, most of the data is not text. The payload of a ping packet contains a pattern of bytes (on Linux, typically the alphabet abcdefghijklmnopqrstuvwxyz...) which can be partially read as ASCII. Everything else — headers, flags, checksums — will appear as dots (.) in the ASCII column, meaning those bytes have no printable ASCII representation.

💡 Why does this matter?
In more advanced analysis — for example when inspecting HTTP, FTP or Telnet traffic — the ASCII column becomes extremely powerful, as those protocols transmit data in plain text. You can literally read usernames, passwords or web requests directly in Wireshark. This is also one of the key reasons why unencrypted protocols are considered a serious security risk.