---
layout: post
title: "Lian_Yu: Try Hack Me Walkthrough"
date: 2020-05-23 20:14:20 +0000
last_modified_at: 2020-05-23 20:14:20 +0000
category: Walkthrough
tags: ["Try Hack Me", Lian_Yu, Weekly Challenge, Steg]
comments: true
image:
  feature: LianYu/LianYuFeature_v3mxtq.jpg
  credit: CW / CW
  creditlink: https://www.cwtv.com/shows/arrow/view-in-app/
---

This post documents the complete walkthrough of Lian_Yu, a weekly vulnerable challenge [VM][1] created by [Deamon][2], and hosted at [Try Hack Me][3]. If you are uncomfortable with spoilers, please stop reading now.
{: .notice}

<!--more-->

## On this post
{:.no_toc}

* TOC
{:toc}

## Background

Lian_Yu is a weekly challenge vulnerable VM from Try Hack Me.

## Information Gathering

Let’s start with a `nmap` scan to establish the open ports in the host.

```
# nmap -vv --reason -Pn -sV -sC --version-all -oN /root/Desktop/THM/Lian_Yu/scans/tcp_nmap.txt 10.10.15.205
...
Nmap scan report for 10.10.15.205
Host is up, received user-set (0.098s latency).
Scanned at 2020-05-22 17:09:51 EDT for 12s
Not shown: 996 closed ports
Reason: 996 resets
PORT    STATE SERVICE REASON         VERSION
21/tcp  open  ftp     syn-ack ttl 63 vsftpd 3.0.2
22/tcp  open  ssh     syn-ack ttl 63 OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
| ssh-hostkey: 
|   1024 56:50:bd:11:ef:d4:ac:56:32:c3:ee:73:3e:de:87:f4 (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBAOZ67Cx0AtDwHfVa7iZw6O6htGa3GHwfRFSIUYW64PLpGRAdQ734COrod9T+pyjAdKscqLbUAM7xhSFpHFFGM7NuOwV+d35X8CTUM882eJX+t3vhEg9d7ckCzNuPnQSpeUpLuistGpaP0HqWTYjEncvDC0XMYByf7gbqWWU2pe9HAAAAFQDWZIJ944u1Lf3PqYCVsW48Gm9qCQAAAIBfWJeKF4FWRqZzPzquCMl6Zs/y8od6NhVfJyWfi8APYVzR0FR05YCdS2OY4C54/tI5s6i4Tfpah2k+fnkLzX74fONcAEqseZDOffn5bxS+nJtCWpahpMdkDzz692P6ffDjlSDLNAPn0mrJuUxBFw52Rv+hNBPR7SKclKOiZ86HnQAAAIAfWtiPHue0Q0J7pZbLeO8wZ9XNoxgSEPSNeTNixRorlfZBdclDDJcNfYkLXyvQEKq08S1rZ6eTqeWOD4zGLq9i1A+HxIfuxwoYp0zPodj3Hz0WwsIB2UzpyO4O0HiU6rvQbWnKmUaH2HbGtqJhYuPr76XxZtwK4qAeFKwyo87kzg==
|   2048 39:6f:3a:9c:b6:2d:ad:0c:d8:6d:be:77:13:07:25:d6 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDRbgwcqyXJ24ulmT32kAKmPww+oXR6ZxoLeKrtdmyoRfhPTpCXdocoj0SqjsETI8H0pR0OVDQDMP6lnrL8zj2u1yFdp5/bDtgOnzfd+70Rul+G7Ch0uzextmZh7756/VrqKn+rdEVWTqqRkoUmI0T4eWxrOdN2vzERcvobqKP7BDUm/YiietIEK4VmRM84k9ebCyP67d7PSRCGVHS218Z56Z+EfuCAfvMe0hxtrbHlb+VYr1ACjUmGIPHyNeDf2430rgu5KdoeVrykrbn8J64c5wRZST7IHWoygv5j9ini+VzDhXal1H7l/HkQJKw9NSUJXOtLjWKlU4l+/xEkXPxZ
|   256 a6:69:96:d7:6d:61:27:96:7e:bb:9f:83:60:1b:52:12 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPfrP3xY5XGfIk2+e/xpHMTfLRyEjlDPMbA5FLuasDzVbI91sFHWxwY6fRD53n1eRITPYS1J6cBf+QRtxvjnqRg=
|   256 3f:43:76:75:a8:5a:a6:cd:33:b0:66:42:04:91:fe:a0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDexCVa97Otgeg9fCD4RSvrNyB8JhRKfzBrzUMe3E/Fn
80/tcp  open  http    syn-ack ttl 63 Apache httpd
| http-methods: 
|_  Supported Methods: POST OPTIONS GET HEAD
|_http-server-header: Apache
|_http-title: Purgatory
111/tcp open  rpcbind syn-ack ttl 63 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          38824/udp6  status
|   100024  1          46754/udp   status
|   100024  1          47141/tcp6  status
|_  100024  1          53453/tcp   status
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri May 22 17:10:03 2020 -- 1 IP address (1 host up) scanned in 12.95 seconds

```

### Website Analysis

Appears to be some kind of `http` service. Here's how it looks like.

{% include image.html image_alt="LianYu/LianYuIndex_ockiec.jpg" image_src="LianYu/LianYuIndex_ockiec.jpg" %}

First things first we do a `gobuster` scan.

```
gobuster dir -u http://10.10.15.205 -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
```

Once the scan returns we see one result.

{% include image.html image_alt="LianYu/LianYuFirstDirb_pmtvd5.jpg" image_src="LianYu/LianYuFirstDirb_pmtvd5.jpg" %}

Going to that page we view the source of the page. We see a comment about a keyword that we will need to use at a later time.

{% include image.html image_alt="LianYu/LianYuCodeword_bj2nmr.jpg" image_src="LianYu/LianYuCodeword_bj2nmr.jpg" %}

We run another `gobuster` from this new directory. This will hopefully show us more of the path.

```
gobuster dir -u http://10.10.15.205/diryoufound/ -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
```

It looks like the scan returns a new directory from this path.

{% include image.html image_alt="LianYu/LianYuSecondDirb_s205xw.jpg" image_src="LianYu/LianYuSecondDirb_s205xw.jpg" %}

Viewing the source of this page till us that we need a file that end with a certain extension. We can start a `wfuzz` scan to see if maybe we can find a file that ends in that extension.

{% include image.html image_alt="LianYu/LianYuExtension_j3ho3a.jpg" image_src="LianYu/LianYuExtension_j3ho3a.jpg" %}

```
wfuzz -u http://10.10.15.205/firstdirc/seconddir/FUZZ.extension -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -c --hc 404
```

{% include image.html image_alt="LianYu/LianYuWfuzz_kfloph.jpg" image_src="LianYu/LianYuWfuzz_kfloph.jpg" %}

We see one result that comes up that we go and find a string on that page. It looks like it was password that was base58 encoded. I ran it through cyberchef to get the password for the `FTP` service.
Using the codeword we found earlier. We can log into the `FTP` service.

{% include image.html image_alt="LianYu/LianYuSecert_vsegfc.jpg" image_src="LianYu/LianYuSecert_vsegfc.jpg" %}

## Exploiting the Box

### Getting `user.txt`

```
ftp 10.10.15.205
```

{% include image.html image_alt="LianYu/LianYuFTP_pow2oh.jpg" image_src="LianYu/LianYuFTP_pow2oh.jpg" %}

It looks like we have three images. Two are png and one is jpg. I tried opening up the file `Leave_me_alone.jpg` It looks like this file is currently corrupted. 
I opened the image in hexeditor and noticed that the first 6 hex characters are not correct for png file. I changed it to 89 50 4E 47 0D 0A. 

{% include image.html image_alt="LianYu/LianYuHexEdit_bzdzjh.jpg" image_src="LianYu/LianYuHexEdit_bzdzjh.jpg" %}

We could open the image and see it gave us a new password.

{% include image.html image_alt="LianYu/LianYuFixedImage_ksoxrz.jpg" image_src="LianYu/LianYuFixedImage_ksoxrz.jpg" %}

Now we can try this password on aa.jpg and `steghide`

```
steghide extract --sf aa.jpg
```

This see a hidden zip with in the file. We extract the two files from zip and one of them contains two user-names and the other contains a password. 
We try both and turns out we can login via ssh using one of them.

We ssh in and get our user.txt flag

{% include image.html image_alt="LianYu/LianYuUser_j3emoi.jpg" image_src="LianYu/LianYuUser_j3emoi.jpg" %}

### Getting `root.txt`

We do our standard enumeration and do sudo -l. We find out we can run `pkexec` as sudo

{% include image.html image_alt="LianYu/LianYuEnum_cg8jbf.jpg" image_src="LianYu/LianYuEnum_cg8jbf.jpg" %}

```
sudo /usr/bin/pkexec /bin/bash
```

Running this gives us root as pkexec allows users to run applications as other users. Usually you need to specify the users password but because of this misconfiguration we can run sudo on it without a password. 
Doing this we see our root.txt flag!

{% include image.html image_alt="LianYu/LianYuRoot_qjeyxv.jpg" image_src="LianYu/LianYuRoot_qjeyxv.jpg" %}

:dancer:

[1]: https://tryhackme.com/room/lianyu
[2]: https://tryhackme.com/p/Deamon
[3]: https://tryhackme.com
