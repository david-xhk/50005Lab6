# Lab 6: Internet Domain Name System

## Table of Contents

- [Lab 6: Internet Domain Name System](#lab-6-internet-domain-name-system)
  * [Overview](#overview)
  * [Part 1: Exploring DNS using `dig`](#part-1-exploring-dns-using-dig)
    + [DNS Basics](#dns-basics)
      - [Question 1](#question-1)
        * [Answer](#answer)
        * [Outputs](#outputs)
      - [Question 2](#question-2)
        * [Answer](#answer-1)
      - [Question 3](#question-3)
        * [Answer](#answer-2)
      - [Question 4](#question-4)
        * [Answer](#answer-3)
        * [Outputs](#outputs-1)
      - [Question 5](#question-5)
        * [Answer](#answer-4)
        * [Outputs](#outputs-2)
    + [Understanding Hierarchy](#understanding-hierarchy)
      - [Question 6](#question-6)
        * [Answer](#answer-5)
        * [Outputs](#outputs-3)
      - [Question 7](#question-7)
        * [Answer](#answer-6)
        * [Outputs](#outputs-4)
    + [Understanding Caching](#understanding-caching)
      - [Question 8](#question-8)
        * [Answer](#answer-7)
        * [Outputs](#outputs-5)
      - [Question 9](#question-9)
        * [Answer](#answer-8)
        * [Outputs](#outputs-6)
      - [Question 10](#question-10)
        * [Answer](#answer-9)
        * [Outputs](#outputs-7)
  * [Part 2: Tracing DNS using Wireshark](#part-2-tracing-dns-using-wireshark)
    + [Question 1](#question-1-1)
      - [Answer](#answer-10)
      - [Output](#output)
    + [Question 2](#question-2-1)
      - [Answer](#answer-11)
      - [Output](#output-1)
    + [Question 3](#question-3-1)
      - [Answer](#answer-12)
      - [Output](#output-2)
    + [Question 4](#question-4-1)
      - [Answer](#answer-13)
      - [Output](#output-3)
    + [Question 5](#question-5-1)
      - [Answer](#answer-14)
      - [Output](#output-4)
    + [Question 6](#question-6-1)
      - [Answer](#answer-15)
      - [Output](#output-5)


## Overview

*In NS Module 4, we learnt about the role of the Domain Name System (DNS) in Internet naming and addressing.*

*In this lab exercise, we will go deeper into DNS by using specialised network tools to perform and analyse DNS queries.*


## Part 1: Exploring DNS using `dig`

*The Domain Information Groper (`dig`) is commonly used for performing DNS lookups*


### DNS Basics

#### Question 1

*Using `dig`, find the IP address for `thyme.lcs.mit.edu.` What is the IP address?*

##### Answer

- The IP address of `thyme.lcs.mit.edu.` was found to be `18.26.0.122`.

##### Outputs

> **Output for `dig thyme.lcs.mit.edu.`**
> ```
> users-MacBook-Pro:~ user$ dig thyme.lcs.mit.edu.
> 
> ; <<>> DiG 9.10.6 <<>> thyme.lcs.mit.edu.
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17154
> ;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 512
> ;; QUESTION SECTION:
> ;thyme.lcs.mit.edu.		IN	A
> 
> ;; ANSWER SECTION:
> thyme.lcs.mit.edu.	1799	IN	CNAME	mercury.lcs.mit.edu.
> mercury.lcs.mit.edu.	1799	IN	A	18.26.0.122
> 
> ;; Query time: 535 msec
> ;; SERVER: 192.168.2.101#53(192.168.2.101)
> ;; WHEN: Thu Apr 16 02:05:51 +08 2020
> ;; MSG SIZE  rcvd: 84
> ```


#### Question 2

*The `dig` answer for the previous question includes a record of type `CNAME`. What does `CNAME` mean?*

##### Answer

- `CNAME` stands for **canonical name**. It is used to redirect a domain name to another domain name.

- For instance, `thyme.lcs.mit.edu.` has a `CNAME` record with value `mercury.lcs.mit.edu.`.

  - This means that the domain `thyme.lcs.mit.edu.` should be redirected to `mercury.lcs.mit.edu.`.


#### Question 3

*What is the expiration time for the `CNAME` record?*

##### Answer

- The expiration time for the `CNAME` record was **1799 seconds**.

  - This can be found in the second column of the `CNAME` record (See [above]#outputs).


#### Question 4

*Run the following commands to find out what your computer receives when it looks up `ai` and `ai.` in the `mit.edu` domain.*

- *`dig +domain=mit.edu ai`*

- *`dig +domain=mit.edu ai.`*

*What are the two resulting IP addresses?*

##### Answer

- The first command `dig +domain=mit.edu ai` did not result in an IP address.

  - An `SOA` record was returned instead.
  
    - `SOA` (**Start Of Authority**) records contain administrative information about a zone.

- The second command `dig +domain=mit.edu ai.` returned the IP address `209.59.119.34`.

##### Outputs

> **Output for `dig +domain=mit.edu ai`**  
> ```
> users-MacBook-Pro:~ user$ dig +domain=mit.edu ai
> 
> ; <<>> DiG 9.10.6 <<>> +domain=mit.edu ai
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45448
> ;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 512
> ;; QUESTION SECTION:
> ;ai.mit.edu.			IN	A
> 
> ;; AUTHORITY SECTION:
> ai.mit.edu.		1507	IN	SOA	auth-ns0.csail.mit.edu. bug-domain.csail.mit.edu. 35472 1800 300 604800 14400
> 
> ;; Query time: 4 msec
> ;; SERVER: 192.168.2.100#53(192.168.2.100)
> ;; WHEN: Thu Apr 16 02:19:58 +08 2020
> ;; MSG SIZE  rcvd: 101
> ```

> **Output for `dig +domain=mit.edu ai.`**  
> ```
> users-MacBook-Pro:~ user$ dig +domain=mit.edu ai.
> 
> ; <<>> DiG 9.10.6 <<>> +domain=mit.edu ai.
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 39815
> ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 512
> ;; QUESTION SECTION:
> ;ai.				IN	A
> 
> ;; ANSWER SECTION:
> ai.			30905	IN	A	209.59.119.34
> 
> ;; Query time: 3 msec
> ;; SERVER: 192.168.2.100#53(192.168.2.100)
> ;; WHEN: Thu Apr 16 02:20:32 +08 2020
> ;; MSG SIZE  rcvd: 47
> ```


#### Question 5

*Why are the results for both queries different? Look up the manual for `dig` to find out what the `+domain` parameter does.*

*Based on the output of the two commands, what is the difference between the DNS searches being performed for `ai` and `ai.`?*

##### Answer

- `+domain` is a query option that modifies a query to search within a specific domain.

- By specifying `+domain`, `dig` will only search within the zone specified under `+domain` for a given domain name.

- Thus, the command `dig +domain=mit.edu ai` will search for the domain name `ai` under the zone `mit.edu`.

  - This happens to be the domain for the now-defunct [MIT Artificial Intelligence Laboratory](http://www.ai.mit.edu/publications/).
  
    - The MIT Artificial Intelligence Laboratory and the MIT Laboratory for Computer Science merged to form CSAIL on July 1, 2003.
  
    - Visiting `ai.mit.edu` on a web browser will actually redirect to the [CSAIL website](https://www.csail.mit.edu).
  
  - Since `dig +domain=mit.edu ai` only returned an `SOA` record, we can conclude that `ai.mit.edu` does not have any `A` records

- Meanwhile, the command `dig +domain=mit.edu ai.` will search for the domain name `ai.` under the zone `mit.edu`.

- However, because `ai.` ends with a `.`, it is treated as a **top-level domain** which is queried from the root domain `.`.

  - `ai` happens to be the country code top-level domain for the country of Anguilla.
  
  - `ai.` actually hosts a [webpage](https://ai.) containing links to [Anguilla domain registration](https://nic.com.ai).

    - From the `A` record that was returned, we can see that this webpage is hosted at IP `209.59.119.34`.

##### Outputs

> **Output for `man dig`**
> ```
> NAME
>        dig - DNS lookup utility
> 
> ...
> 
> ...
> 
> QUERY OPTIONS
>        dig provides a number of query options which affect the way in which
>        lookups are made and the results displayed...
>        ...
> 
>        ...
> 
>        +domain=somename
>            Set the search list to contain the single domain somename, as if
>            specified in a domain directive in /etc/resolv.conf, and enable
>            search list processing as if the +search option were given.
> 
>        ...
> 
> ...
> 
> ...
> ```


### Understanding Hierarchy

*In the previous section, you ran `dig` without changing the default options.*

*This causes `dig` to perform a recursive lookup if the DNS server being queried supports it.*

*Now, you will trace the intermediate steps involved in a performing recursive query by beginning at a root server and manually going through the DNS hierarchy to resolve a host name.*

*You can obtain a list of all the root servers by running the command `dig . NS`.*


#### Question 6

*Use `dig` to query one of the DNS root servers for the IP address of `lirone.csail.mit.edu` without using recursion.*

*What is the command that you use to do this?*

##### Answer

- I used the command `dig @a.root-servers.net. lirone.csail.mit.edu +norecurs`.

##### Outputs

> **Output for `dig @a.root-servers.net. lirone.csail.mit.edu +norecurs`**
> ```
> users-MacBook-Pro:~ user$ dig @a.root-servers.net. lirone.csail.mit.edu +norecurs
> 
> ; <<>> DiG 9.10.6 <<>> @a.root-servers.net. lirone.csail.mit.edu +norecurs
> ; (1 server found)
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 48552
> ;; flags: qr; QUERY: 1, ANSWER: 0, AUTHORITY: 13, ADDITIONAL: 27
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 4096
> ;; QUESTION SECTION:
> ;lirone.csail.mit.edu.		IN	A
> 
> ;; AUTHORITY SECTION:
> ...
> edu.			172800	IN	NS	a.edu-servers.net.
> ...
> 
> ;; ADDITIONAL SECTION:
> ...
> a.edu-servers.net.	172800	IN	A	192.5.6.30
> ...
> 
> ;; Query time: 199 msec
> ;; SERVER: 198.41.0.4#53(198.41.0.4)
> ;; WHEN: Thu Apr 16 03:57:24 +08 2020
> ;; MSG SIZE  rcvd: 844
> ```


#### Question 7

*Go through the DNS hierarchy from the root until you have found the IP address of `lirone.csail.mit.edu`.*

*You should disable recursion and follow the referrals manually.*

*Which commands did you use, and what address did you find?*

##### Answer

- The commands I used were:

  1. `dig @a.root-servers.net. lirone.csail.mit.edu +norecurs`
  
  2. `dig @a.edu-servers.net. lirone.csail.mit.edu +norecurs`
  
  3. `dig @asia1.akam.net. lirone.csail.mit.edu +norecurs`
  
  4. `dig @auth-ns0.csail.mit.edu. lirone.csail.mit.edu +norecurs`

- I found that the IP address of `lirone.csail.mit.edu` is `128.52.129.186`.

##### Outputs

> **Output for `dig @a.edu-servers.net. lirone.csail.mit.edu +norecurs`**
> ``` 
> users-MacBook-Pro:~ user$ dig @a.edu-servers.net. lirone.csail.mit.edu +norecurs
> 
> ; <<>> DiG 9.10.6 <<>> @a.edu-servers.net. lirone.csail.mit.edu +norecurs
> ; (1 server found)
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 56161
> ;; flags: qr; QUERY: 1, ANSWER: 0, AUTHORITY: 8, ADDITIONAL: 1
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 4096
> ;; QUESTION SECTION:
> ;lirone.csail.mit.edu.		IN	A
> 
> ;; AUTHORITY SECTION:
> ...
> mit.edu.		172800	IN	NS	asia1.akam.net.
> ...
> 
> ;; Query time: 42 msec
> ;; SERVER: 192.5.6.30#53(192.5.6.30)
> ;; WHEN: Thu Apr 16 04:01:23 +08 2020
> ;; MSG SIZE  rcvd: 216
> ```

> **Output for `dig @asia1.akam.net. lirone.csail.mit.edu +norecurs`**
> ```
> users-MacBook-Pro:~ user$ dig @asia1.akam.net. lirone.csail.mit.edu +norecurs
> 
> ; <<>> DiG 9.10.6 <<>> @asia1.akam.net. lirone.csail.mit.edu +norecurs
> ; (1 server found)
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43867
> ;; flags: qr; QUERY: 1, ANSWER: 0, AUTHORITY: 4, ADDITIONAL: 6
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 4096
> ;; QUESTION SECTION:
> ;lirone.csail.mit.edu.		IN	A
> 
> ;; AUTHORITY SECTION:
> ...
> csail.mit.edu.		1800	IN	NS	auth-ns0.csail.mit.edu.
> ...
> 
> ;; ADDITIONAL SECTION:
> ...
> auth-ns0.csail.mit.edu.	1800	IN	A	128.30.2.123
> ...
> 
> ;; Query time: 6 msec
> ;; SERVER: 95.100.175.64#53(95.100.175.64)
> ;; WHEN: Thu Apr 16 04:01:55 +08 2020
> ;; MSG SIZE  rcvd: 233
> ```

> **Output for `dig @auth-ns0.csail.mit.edu. lirone.csail.mit.edu +norecurs`**
> ```
> users-MacBook-Pro:~ user$ dig @auth-ns0.csail.mit.edu. lirone.csail.mit.edu +norecurs
> 
> ; <<>> DiG 9.10.6 <<>> @auth-ns0.csail.mit.edu. lirone.csail.mit.edu +norecurs
> ; (1 server found)
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 27754
> ;; flags: qr aa; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 4096
> ;; QUESTION SECTION:
> ;lirone.csail.mit.edu.		IN	A
> 
> ;; ANSWER SECTION:
> lirone.csail.mit.edu.	1800	IN	A	128.52.129.186
> 
> ;; Query time: 339 msec
> ;; SERVER: 128.30.2.123#53(128.30.2.123)
> ;; WHEN: Thu Apr 16 04:02:33 +08 2020
> ;; MSG SIZE  rcvd: 65
> ```


### Understanding Caching

#### Question 8

*Without using recursion, query your default DNS server for information about `www.dmoz.org` and answer the following questions.*

- *What is the command that you used?*

- *Did your default server have the answer in its cache? How did you know?*

- *How long did the query take?*

*Note: If the information was cached, find another host name that was not cached and complete all the questions in this section using that host.*

##### Answer

- I used the command `dig www.who.org +norecurs`.
  
- My default server did not have the answer in its cache.

  - This is because there was no answer section in the `dig` response.
  
  - Instead, information about the top-level domain `org` was returned in the authority section.
  
- The query only took 4 milliseconds.

  - This is because information about common top-level domains such as `org` is usually cached by local DNS name servers.

##### Outputs

> **Output for `dig www.who.org +norecurs`**
> ```
> users-MacBook-Pro:~ user$ dig www.who.org +norecurs
> 
> ; <<>> DiG 9.10.6 <<>> www.who.org +norecurs
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 2275
> ;; flags: qr ra; QUERY: 1, ANSWER: 0, AUTHORITY: 6, ADDITIONAL: 10
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 512
> ;; QUESTION SECTION:
> ;www.who.org.			IN	A
> 
> ;; AUTHORITY SECTION:
> ...
> org.			31973	IN	NS	b0.org.afilias-nst.org.
> ...
> 
> ;; ADDITIONAL SECTION:
> ...
> b0.org.afilias-nst.org.	36357	IN	A	199.19.54.1
> ...
> 
> ;; Query time: 4 msec
> ;; SERVER: 192.168.2.100#53(192.168.2.100)
> ;; WHEN: Thu Apr 16 04:33:45 +08 2020
> ;; MSG SIZE  rcvd: 394
> ```


#### Question 9

*Query your default DNS server for information about the host in the previous question, using the recursion option this time.*

*How long did the query take?*

##### Answer

- The query took 430 milliseconds this time.

- This time, an answer was returned.

  - The IP address of `www.who.org.` was found to be `158.232.12.119`.

##### Outputs

> **Output for `dig www.who.org`**
> ```
> users-MacBook-Pro:~ user$ dig www.who.org
> 
> ; <<>> DiG 9.10.6 <<>> www.who.org
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 61394
> ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 512
> ;; QUESTION SECTION:
> ;www.who.org.			IN	A
> 
> ;; ANSWER SECTION:
> www.who.org.		21599	IN	A	158.232.12.119
> 
> ;; Query time: 430 msec
> ;; SERVER: 192.168.2.100#53(192.168.2.100)
> ;; WHEN: Thu Apr 16 04:48:11 +08 2020
> ;; MSG SIZE  rcvd: 56
> ```


#### Question 10

*Query your default DNS server for information about the same host without using recursion.*

*How long did the query take?*

*Has the cache served its purpose? Explain why.*

##### Answer

- This time, the query only took 3 milliseconds.

- The cache has served its purpose.

  - This is because now, the answer can be returned immediately without any need for recursion.

##### Outputs

> **Output for `dig www.who.org +norecurs` after caching**
> ```
> users-MacBook-Pro:~ user$ dig www.who.org +norecurs
> 
> ; <<>> DiG 9.10.6 <<>> www.who.org +norecurs
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32708
> ;; flags: qr ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 512
> ;; QUESTION SECTION:
> ;www.who.org.			IN	A
> 
> ;; ANSWER SECTION:
> www.who.org.		20277	IN	A	158.232.12.119
> 
> ;; Query time: 3 msec
> ;; SERVER: 192.168.2.100#53(192.168.2.100)
> ;; WHEN: Thu Apr 16 05:10:13 +08 2020
> ;; MSG SIZE  rcvd: 56
> ```


## Part 2: Tracing DNS using Wireshark

*Wireshark is a powerful tool used to capture packets sent over a network and analyse the content of the packets retrieved.*

*The file `dnsrealtrace.pcapng` contains a trace of the packets sent and received when a web page is downloaded from a web server over the SUTD network.*

*In the process of downloading the web page, DNS is used to find the IP address of the server.*

*Open the `dnsrealtrace.pcapng` in Wireshark and answer the following questions.*


### Question 1

#### Answer

#### Output


### Question 2

#### Answer

#### Output


### Question 3

#### Answer

#### Output


### Question 4

#### Answer

#### Output


### Question 5

#### Answer

#### Output


### Question 6

#### Answer

#### Output


