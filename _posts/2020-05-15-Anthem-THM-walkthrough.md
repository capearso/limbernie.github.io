---
layout: post
title: "Anthem: Try Hack Me Walkthrough"
date: 2020-05-15 20:20:20 +0000
last_modified_at: 2020-05-15 20:20:20 +0000
category: Walkthrough
tags: ["Try Hack Me", Anthem, Weekly Challenge]
comments: true
image:
  feature: Anthem_knhf8d.png
  credit: THM / TryHackMe
  creditlink: https://tryhackme.com/room/anthem
---

This post documents the complete walkthrough of Anthem, a weekly vulnerable challenge [VM][1] created by [Chevalier][2], and hosted at [Try Hack Me][3]. If you are uncomfortable with spoilers, please stop reading now.
{: .notice}

<!--more-->

## On this post
{:.no_toc}

* TOC
{:toc}

## Background

Anthem is a weekly challange vulnerable VM from Try Hack Me.

## Information Gathering

Let’s start with a `nmap` scan to establish the open ports in the host.

```
# nmap -vv --reason -Pn -sV -sC --version-all -oN /root/Desktop/THM/Anthem/scans/tcp_nmap.txt
...
Increasing send delay for 10.10.63.195 from 0 to 5 due to 169 out of 563 dropped probes since last increase.
Nmap scan report for 10.10.63.195
Host is up, received user-set (0.10s latency).
Scanned at 2020-05-15 15:59:17 EDT for 91s
Not shown: 995 closed ports
Reason: 995 resets
PORT     STATE SERVICE       REASON          VERSION
80/tcp   open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
135/tcp  open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds? syn-ack ttl 127
3389/tcp open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN-LU09299160F
|   NetBIOS_Domain_Name: WIN-LU09299160F
|   NetBIOS_Computer_Name: WIN-LU09299160F
|   DNS_Domain_Name: WIN-LU09299160F
|   DNS_Computer_Name: WIN-LU09299160F
|   Product_Version: 10.0.17763
|_  System_Time: 2020-05-15T20:00:26+00:00
| ssl-cert: Subject: commonName=WIN-LU09299160F
| Issuer: commonName=WIN-LU09299160F
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2020-04-04T22:56:38
| Not valid after:  2020-10-04T22:56:38
| MD5:   2814 61de 95b7 e9b5 4789 3027 7f1f 60d2
| SHA-1: d47d 2a8f 6143 b820 936e 4120 cdd1 9ddc 5385 d285
| -----BEGIN CERTIFICATE-----
| MIIC4jCCAcqgAwIBAgIQObhN9c8QnIVGx+ZslzEOmzANBgkqhkiG9w0BAQsFADAa
| MRgwFgYDVQQDEw9XSU4tTFUwOTI5OTE2MEYwHhcNMjAwNDA0MjI1NjM4WhcNMjAx
| MDA0MjI1NjM4WjAaMRgwFgYDVQQDEw9XSU4tTFUwOTI5OTE2MEYwggEiMA0GCSqG
| SIb3DQEBAQUAA4IBDwAwggEKAoIBAQDA4MPIi4yCYJlBv6vwXF5lu5NbQCPQxk4q
| 7lJsJSvTRSIFi2fVl3l+rWTr69mutnVqo+bMilJorN2B6DqsCJBV+7pITFSICM6b
| +G/sOEblVust2tUU8NLbAiBH9oXhF0P5dIhMzRC4pcZjhCRR+IcOjnABTCkdAchD
| Mf4XQJx6GZOXBCBMXGW/vCKZ0q8gti7Hxs36W1ctbj8/i5obd0k0BonMlvRwKxvi
| 7SS+3NrBpc4XivD23YIqCNzErOB19DV3JqZMvbE+NhLEQA51Au2svYwgoJcIIyEC
| HBuINXeFBB+p5dMwp4wppkHN0CuquUyCBZvIPlDW8SAOAc5tgUOJAgMBAAGjJDAi
| MBMGA1UdJQQMMAoGCCsGAQUFBwMBMAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQsF
| AAOCAQEAAziR6P3nN9/EKLhZqJKgkWP9FyNr9CusD78wem1C5fn9h7SjS1PQEhn1
| Gi50rlcUmII4E8Bnv6g/1QZnZIsPtVzO3bokQfbhTEzWOQ8RScB3ZQ+Tg7xM4duA
| NZdzR1/hjOOmPBV4ih3+AKmbEZ63V3PuJOn2+0/NsGXzGKhaNhlAof58lXkXrt9x
| DvmpyfER7oq/3+kPQhXlNK7VZ/dp26BLFQT12be1yyeVck2n/90pXTxV/COaIdsF
| q7RJPVO+4FCua77sUUSV9E5CL3oOFJT5MjkAMEkoKsU9InWHhA5w+ndQqDgXIb40
| 7b3pD6AiS/ZEvSpzCyeVnDprZxVIaQ==
|_-----END CERTIFICATE-----
|_ssl-date: 2020-05-15T20:00:48+00:00; 0s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 0s, deviation: 0s, median: 0s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 26598/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 61865/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 47039/udp): CLEAN (Timeout)
|   Check 4 (port 33149/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-05-15T20:00:29
|_  start_date: N/A
```

### Flag One and Website Analysis

Appears to be some kind of `http` service. Here's how it looks like.

{% include image.html image_alt="anthem_ehocop.jpg" image_src="anthem_ehocop.jpg" %}

First things first we go to http://10.10.63.195/robots.txt

{% include image.html image_alt="anthemrobots_vemv4o.png" image_src="anthemrobots_vemv4o.png" %}

It looks we found a password or something that should not be in robots.txt at the top. We add this to a list of passwords for later use. We also find out that this system is running a Umbraco CMS.

{% include image.html image_alt="poem_bq2pmn.png" image_src="poem_bq2pmn.png" %}

Looking through the posts we find a poem that was about the Admin of the site. We google that poem and find out it was written by Solomon Grundy. We add them to users on the site to try later. 
In the HR post we see the email format is FL@anthem.com where F is first name and L is last name. So we create the user SG@anthem.com.

{% include image.html image_alt="flag1_a7okeq.png" image_src="flag1_a7okeq.png" %}

Looking at the source code of the hiring page we can find our first flag in <meta content>

### Flag Two

{% include image.html image_alt="flag2_wjcmbn.png" image_src="flag2_wjcmbn.png" %}

We go back to the main page and view the source of website. This leads us to flag number two which can be found in the source code.

### Flag Three

{% include image.html image_alt="flag3_h41kft.png" image_src="flag3_h41kft.png" %}

We click on the we are hiring page and see that Jane Doe is the author and we can navigate to a page called /authors/jane-doe. We follow the link and are awarded are third flag.

### Flag Four

{% include image.html image_alt="flag4_aznzrp.png" image_src="flag4_aznzrp.png" %}

By following the steps for Flag One we can find Flag Four in the meta content on the IT poem page.

### Low-Privilege Shell

{% include image.html image_alt="umbraco_jwou73.png" image_src="umbraco_jwou73.png" %}

Armed with this knowledge, we go to 10.10.63.195/umbraco and try logging in as SG@anthem.com and using the password we found on the robots.txt We are able to log in. 
We can determine the Umbraco version by clining the ? icon and see that we are attacking Umbraco 7.15.4


Bingo.

### Getting `user.txt`

There's another user `robert` (uid=1000). I suppose the file `user.txt` is in his home directory.

{% include image.html image_alt="ffe06c72.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/ffe06c72.png" %}

In `check.txt`, we have our clue how to get `robert`'s password.

```
Encrypting this file with your key should result in out.txt, make sure your key is correct!
```

I suppose when we encrypt `check.txt` with a key (yet to be determined) using `SuperSecureCrypt.py`, we'll get `out.txt`. Here are the relevant `encrypt` and `decrypt` functions `SuperSecureCrypt.py`.

```python
def encrypt(text, key):
    keylen = len(key)
    keyPos = 0
    encrypted = ""
    for x in text:
        keyChr = key[keyPos]
        newChr = ord(x)
        newChr = chr((newChr + ord(keyChr)) % 255)
        encrypted += newChr
        keyPos += 1
        keyPos = keyPos % keylen
    return encrypted

def decrypt(text, key):
    keylen = len(key)
    keyPos = 0
    decrypted = ""
    for x in text:
        keyChr = key[keyPos]
        newChr = ord(x)
        newChr = chr((newChr - ord(keyChr)) % 255)
        decrypted += newChr
        keyPos += 1
        keyPos = keyPos % keylen
    return decrypted
```

It shouldn't be too hard to write a Python script to recover the key based on the "secure" algorithm above. :wink:

<div class="filename"><span>recover.py</span></div>

```python
import sys

key = ''

chk = open(sys.argv[1], 'rb').read().decode('utf-8')
out = open(sys.argv[2], 'rb').read().decode('utf-8')

keylen = int(sys.argv[3])
if (keylen > len(chk) or keylen > len(out)):
    raise ValueError("Key is longer than plaintext or ciphertext")

for x in range(keylen):
    for c in range(256):
        if ((ord(chk[x]) + c) % 255 == ord(out[x])):
            key += chr(c)
            break

print "[*] Key is: %s" % key
```

Simply increase the key size until the key repeats itself.

{% include image.html image_alt="cdf54e7d.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/cdf54e7d.png" %}

The key is `alexandrovichalexandrovich`. :triumph:

Armed with the key, we can utilize `SuperSecureCrypt.py` to get `robert`'s password.

{% include image.html image_alt="baac1320.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/baac1320.png" %}

The password is `SecThruObsFTW`. Let's log in to `robert`'s account and grab that `user.txt`!

{% include image.html image_alt="1fa5dc36.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/1fa5dc36.png" %}

## Privilege Escalation

During enumeration of `robert`'s account, I notice that `robert` is able to `sudo BetterSSH.py` as `root` without password.

{% include image.html image_alt="6d0ae26e.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/6d0ae26e.png" %}

That must be the ticket to `root`! Again, I'm showing the pertinent part where the code is vulnerable.

{% include image.html image_alt="427719f0.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/427719f0.png" %}

It's kinda like a race condition. As long as I'm able to display the contents of whatever that's written to `/tmp/SSH/<random eight chars>`, I'll be able to capture the encrypted password of `root` in `/etc/shadow`. Towards that end, I wrote a simple shell script to monitor for the creation of new file in `/tmp/SSH` and `cat` its content.

<div class="filename"><span>watch.sh</span></div>

```bash
#!/bin/bash

if [ ! -d /tmp/SSH ]; then
  mkdir -p /tmp/SSH
fi

touch /tmp/SSH/ok

while :; do
  find /tmp/SSH -type f -cnewer /tmp/SSH/ok -exec cat {} \;
done
```

I have two terminal windows. On one hand, I had the script watching for new files in `/tmp/SSH`. On the other hand, I just need to `sudo BetterSSH.py`.

{% include image.html image_alt="c115ee36.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/c115ee36.png" %}

Woohoo!

### Getting `root.txt`

Now, it's a matter of firing up John the Ripper to crack the password.

{% include image.html image_alt="6ca9732a.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/6ca9732a.png" %}

Armed with `root`'s password, getting `root.txt` should be a breeze.

{% include image.html image_alt="f003f2f9.png" image_src="/471fc23c-9a1d-4b65-974a-92b516907d9f/f003f2f9.png" %}

:dancer:

[1]: https://tryhackme.com/room/anthem
[2]: https://tryhackme.com/p/Chevalier
[3]: https://tryhackme.co
