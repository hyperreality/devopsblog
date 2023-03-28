---
layout: post
title: Communicating Between Firecracker microVMs using Linux Bridges
---


The [Firecracker doc on network setup](https://github.com/firecracker-microvm/firecracker/blob/main/docs/network-setup.md) works well for basic use cases. The doc shows you how to communicate between your host and Firecracker VMs, firstly by using a TAP device then in the advanced case by using a bridge device.

Up until now, I've only needed to use host-to-guest communication. But, I now want to enable VMs to network with each other. I created a bridge with its own /24, then attached two TAP devices to it, and launched microVMs configured with IPs in that subnet. Host-to-guest networking still worked, but networking between the VMs did not.

### Unicasts not welcome here

More specifically, broadcast packets were being forwarded over the bridge, but not normal unicast packets. For instance I noticed that ARP requests (which are broadcasted to all hosts in a LAN) from one microVM were reaching the other VM. This was seen in the destination VM's ARP table and in `tcpdump`. But the ARP replies which are unicasted from the receiving host to the requestor were getting dropped between the bridge and the requesting VM. This was odd because the packet MAC addresses matched the TAP devices and everything seemed to be configured correctly.

I found that nobody on the Internet seems to know how to fix this either. There's an [issue on the Firecracker GitHub](https://github.com/firecracker-microvm/firecracker-demo/issues/18) and posts on StackExchange but no guide or hints anywhere as to how to get Firecracker-to-Firecracker communication working. In fact there's lots of questions on StackExchange about this bridge behaviour in general, where only broadcasts make it through. Unfortunately the answer was usually that an `iptables` or `arptables` or `ebtables` rule was interfering, or that a particular `sysctl` setting had been overlooked (e.g. `net.ipv4.ip_forward`), none of which applied in my case.

### Turning the bridge into a pseudo-hub

After discussing with a friend, I decided that an approach to try was to flush the MAC table of the bridge. Linux bridges behave like switches so with an empty MAC table it should flood all its ports until it knows on which port it can find a device.

Here's the setup, `8f4b9e2676` is the bridge and `8f4b9e2676_1` and `8f4b9e2676_2` are TAP devices attached to microVMs with internally assigned IP addresses `10.0.80.2` and `10.0.80.3` respectively.

```
$ ip a
34: 8f4b9e2676: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 32:a4:c3:78:36:a7 brd ff:ff:ff:ff:ff:ff
    inet 10.0.80.1/24 scope global 8f4b9e2676
       valid_lft forever preferred_lft forever
    inet6 fe80::30a4:c3ff:fe78:36a7/64 scope link
       valid_lft forever preferred_lft forever
35: 8f4b9e2676_1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master 8f4b9e2676 state UP group default qlen 1000
    link/ether 56:ea:d1:d7:a1:f4 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::54ea:d1ff:fed7:a1f4/64 scope link
       valid_lft forever preferred_lft forever
36: 8f4b9e2676_2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master 8f4b9e2676 state UP group default qlen 1000
    link/ether 22:19:21:28:85:56 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::2019:21ff:fe28:8556/64 scope link
       valid_lft forever preferred_lft forever
```

With `brctl` you can see the MAC addresses of the interfaces connected to the ports of the bridge:

```
$ sudo brctl showmacs 8f4b9e2676
port no mac addr                is local?       ageing timer
  2     22:19:21:28:85:56       yes                0.00
  2     22:19:21:28:85:56       yes                0.00
  1     56:ea:d1:d7:a1:f4       yes                0.00
  1     56:ea:d1:d7:a1:f4       yes                0.00
```

A few [handy guides](https://techglimpse.com/convert-linux-bridge-hub-vm-interospection/) say to use `brctl setageing 0` in order to make the bridge act like a hub. But, I found it only worked to remove learned MAC addresses, not to remove the MAC addresses marked as `yes` under `is local?`. Still, it's an important first step although the modern solution is to append `ageing 0` to the `ip link` command when creating the bridge.

After further research, but there's really not a lot of good resources on this, it's possible to use the `bridge fdb` command to manipulate the routing table at a lower level:

```
$ sudo bridge fdb show | grep 8f4b9e2676
33:33:00:00:00:01 dev 8f4b9e2676 self permanent
01:00:5e:00:00:6a dev 8f4b9e2676 self permanent
33:33:00:00:00:6a dev 8f4b9e2676 self permanent
01:00:5e:00:00:01 dev 8f4b9e2676 self permanent
01:00:5e:00:00:fb dev 8f4b9e2676 self permanent
33:33:ff:78:36:a7 dev 8f4b9e2676 self permanent
33:33:00:00:00:fb dev 8f4b9e2676 self permanent
32:a4:c3:78:36:a7 dev 8f4b9e2676 vlan 1 master 8f4b9e2676 permanent
32:a4:c3:78:36:a7 dev 8f4b9e2676 master 8f4b9e2676 permanent
56:ea:d1:d7:a1:f4 dev 8f4b9e2676_1 vlan 1 master 8f4b9e2676 permanent
56:ea:d1:d7:a1:f4 dev 8f4b9e2676_1 master 8f4b9e2676 permanent
33:33:00:00:00:01 dev 8f4b9e2676_1 self permanent
01:00:5e:00:00:01 dev 8f4b9e2676_1 self permanent
33:33:ff:d7:a1:f4 dev 8f4b9e2676_1 self permanent
33:33:00:00:00:fb dev 8f4b9e2676_1 self permanent
22:19:21:28:85:56 dev 8f4b9e2676_2 vlan 1 master 8f4b9e2676 permanent
22:19:21:28:85:56 dev 8f4b9e2676_2 master 8f4b9e2676 permanent
33:33:00:00:00:01 dev 8f4b9e2676_2 self permanent
01:00:5e:00:00:01 dev 8f4b9e2676_2 self permanent
33:33:ff:28:85:56 dev 8f4b9e2676_2 self permanent
33:33:00:00:00:fb dev 8f4b9e2676_2 self permanent
```

And delete the routes that set the bridge as the master for the TAP devices:

```
$ sudo bridge fdb del 56:ea:d1:d7:a1:f4 dev 8f4b9e2676_1 master
$ sudo bridge fdb del 22:19:21:28:85:56 dev 8f4b9e2676_2 master
$ sudo brctl showmacs 8f4b9e2676
port no mac addr                is local?       ageing timer
```

It's also necessary to configure the `bridge-nf-call-iptables` kernel parameter. This disables packets from passing over the bridge from being processed by `iptables`. I don't understand why this needs to be done, since I removed all `iptables` rules and set all policies to `ACCEPT` and yet networking still didn't work until I set it:

`sudo sysctl -w net.bridge.bridge-nf-call-iptables=0`

And now, amazingly, the inter-microVM communication works! From inside the `10.0.80.2` VM:

```
$ ping 10.0.80.3
PING 10.0.80.3 (10.0.80.3) 56(84) bytes of data.
64 bytes from 10.0.80.3: icmp_seq=1 ttl=64 time=1.37 ms
```

`tcpdump` of the bridge shows the packets going back and forth as expected. This feels like a massive hack and I'm not sure why it works when the correct MAC entries didn't, but until I figure out what was going wrong originally, it will do the job. I only want to connect a maximum of two Firecracker VMs together, so the flooding won't result in extra network traffic.

