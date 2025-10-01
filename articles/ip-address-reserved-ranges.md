---
title: "IP Address Resereved Ranges"
emoji: "☁️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["network"]
published: true
---

# IP Address Reserved And Special use.

インターネットで使われる IP Address でも、色んな都合や事情な物があります。基本情報処理でも出てくるローカルIP( 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 RFC 1918)や ブロードキャストアドレス (255.255.255.255, 255 in any part RFC 919) default route ( 0.0.0.0 RFC 1700)や Loopback Address( 127.0.0.0/8 )がありますけど、それ以外の特殊的な意味合いを持つアドレス。

多分、学生時代に習ったけど、卒業してからも増えた物とかあるので、その補正的な学習。
Docker network の中を見てたら 0.2.21.10 とかが出てきてなんじゃこれとなって RFC とかを当たったメモです。
IPv6 まわりはちょっと追いきれてない。

- see: https://www.speedguide.net/ip/0.2.21.10
- see: https://datatracker.ietf.org/doc/rfc6890/
- see: https://datatracker.ietf.org/doc/rfc8190/


## IPv4

| IP Range | description | RFC |
| :------  | :---------- | :--- |
| 0.0.0.0/8 | Current network (only valid as source address) | RFC 1700 |
| 100.64.0.0 ~ 100.127.255.255 (100.64.0.0/10 prefix) | carrier-grade NAT communication between service provider and subscribers / The Shared Address Space address range | rfc6598 |
| 127.0.0.0 | reserved for loopback and IPC on the localhost. | RFC1700 |
| 127.0.0.1 ~ 127.255.255.254 (127.0.0.0/8) | loopback IP addresses (refers to self) | RFC1700 |
| 192.0.0.0/24 | reserved (IANA) | RFC 5735 |
| 192.0.2.0/24 | TEST-NET-1 | RFC1700 |
| 192.88.99.0/24 |  IPv6 to IPv4 relay. | RFC 3068 |
| 198.18.0.0/15  | network benchmark tests | RFC 2544 |
| 198.51.100.0/24 | TEST-NET-2.  | RFC 5737 |
| 203.0.113.0/24  | TEST-NET-3 | RFC 5737 |
| 224.0.0.0 ~ 239.255.255.255 (224.0.0.0/4) | reserved for multicast addresses | RFC 3171 |
| 240.0.0.0/4  | reserved (former Class E network) | RFC 1700 |
| 255.255.255.255/32 | the limited broadcast address (limited to all other nodes on the LAN) | RFC 919, rfc8190 |
| 0.0.0.0 |  routing context means the default route (to "the rest of" the internet) | RFC 1700 |
| 0.0.0.0 | the context of firewalls means "all addresses of the local machine" | RFC 1700 |


## IPv6

| IP Range | description | RFC |
| :------  | :---------- | :--- |
| ::1/128 | Loopback Address | RFC4291 |
| ::/128   | Unspecified Address | RFC4291 |
| 64:ff9b::/96 | IPv4-IPv6 Translat | RFC6052 |
| ::ffff:0:0/96 | IPv4-mapped Address | RFC4291 |
| 100::/64 | Discard-Only Address Block | RFC6666 |
| 2001::/23 | IETF Protocol Assignments / Unless allowed by a more specific allocation | RFC2928 |
| 2001::/32 | TEREDO[[ Teredo とは、複数段の IPv4 NAT 環境の背後にある端末が、UDP を用いたトンネリングによって IPv6 接続を確立する仕組み]] | RFC4380 |
| 2001:2::/48 | Benchmarking | RFC5180 |
| 2001:db8::/32 | Documentation | RFC3849 |
| 2001:10::/28 | ORCHID | RFC4843 |
| 2002::/16 | 6to4 | RFC3056 |
| fc00::/7 | Unique-Local -- False [7] | RFC4193 |
| fe80::/10 | Linked-Scoped Unicast | RFC4291 |





