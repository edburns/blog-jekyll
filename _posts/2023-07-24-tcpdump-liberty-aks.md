---
layout: post
title:  Diagnose outbound database connection problems with Liberty on Azure
date:   2023-07-24 13:10-0400
comments: true
---

Cross posted at [dev.to]().

This self-contained blog post shows how you to use `tcpdump` to troubleshoot and diagnose outbound network connections from a local Liberty server to an Azure SQL Database.

I was working on validating the steps in [Deploy a Java application with Open Liberty or WebSphere Liberty on an Azure Kubernetes Service (AKS) cluster](https://learn.microsoft.com/en-us/azure/aks/howto-deploy-java-liberty-app) for use in an upcoming conference workshop. The workshop students would be given access to their own Ubuntu VMs and also their own Azure credits to execute the steps of the workshop. This is a very common pattern I have used many times in the past.

While preparing the VM, the hosting environment for the VM started having trouble connecting to the external Internet, and thus could not access the Azure SQL Database. When starting `mvn liberty:run`, I observed this error.

```bash
[INFO] [ERROR   ] CWWJP0015E: An error occurred in the org.eclipse.persistence.jpa.PersistenceProvider persistence provider when it attempted to create the container entity manager factory for the coffees persistence unit. The following error occurred: Exception [EclipseLink-4002] (Eclipse Persistence Services - 2.7.11.v20220804-52dea2a3c0): org.eclipse.persistence.exceptions.DatabaseException
[INFO] Internal Exception: java.sql.SQLException: The TCP/IP connection to the host rwo010720nxf.database.windows.net, port 1433 has failed. Error: "Connect timed out. Verify the connection properties. Make sure that an instance of SQL Server is running on the host and accepting TCP/IP connections at the port. Make sure that TCP connections to the port are not blocked by a firewall.". DSRA0010E: SQL State = 08S01, Error Code = 0
[INFO] Error Code: 0
```

However, when I ran `mvn liberty:run` on my local laptop with the same sample app code, I observed it was able to successfully connect to the database. Before concluding it was [Somebody Else's Problem](https://hitchhikers.fandom.com/wiki/Somebody_Else%27s_Problem_Field), I wanted to rule out any local firewall configuration on the VM. 

This was a matter of asking `firewall-cmd` to `--list-all` rules.

```bash
# firewall-cmd --list-all
  target: default
  icmp-block-inversion: no
  interfaces: ens192
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 
  protocols: 
  forward: no
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```

Looks like no rules at all. That's not it.

Let's look at outbound traffic on TCP port 1433.

I forget where I found this tip, so I am recording it in this blog post for my own later referral. Yes, it's obvious: `tcpdump`. There are probably better and more efficient ways to do this. But I work on so many different things at so many different levels of abstraction, I can't remember the specifics. I have learned from rock star programmers I know: they accept their limitations and write things down.

### tcpdump on the VM

First we need to identify the network interface name. That's `ifconfig -a`:

```bash
[ibmuser@student java-app]$ ifconfig -a
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:14:3c:39:f9  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.10.2  netmask 255.255.255.0  broadcast 10.0.10.255
        inet6 fe80::250:56ff:feb4:550c  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b4:55:0c  txqueuelen 1000  (Ethernet)
        RX packets 591136  bytes 846561034 (807.3 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 21664  bytes 2609977 (2.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 41  bytes 2429 (2.3 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 41  bytes 2429 (2.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

Good news, that one is obvious. It must be `ens192`.

Then, run the magic `tcpdump -i ens192 port 1433`. This will hang indefinitely. This will show nothing untill some traffic appears on port 1433.

In a separate terminal, restart the app with `mvn liberty:run`. This caused the following output to appear in the `tcpdump` window.

```bash
tcpdump -i ens192 port 1433
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on ens192, link-type EN10MB (Ethernet), capture size 262144 bytes
12:43:36.963152 IP student.51002 > 40.79.153.12.ms-sql-s: Flags [S], seq 2117028828, win 29200, options [mss 1460,sackOK,TS val 2330981707 ecr 0,nop,wscale 7], length 0
12:43:37.563057 IP student.51008 > 40.79.153.12.ms-sql-s: Flags [S], seq 3629156152, win 29200, options [mss 1460,sackOK,TS val 2330982307 ecr 0,nop,wscale 7], length 0
12:43:38.626345 IP student.51008 > 40.79.153.12.ms-sql-s: Flags [S], seq 3629156152, win 29200, options [mss 1460,sackOK,TS val 2330983371 ecr 0,nop,wscale 7], length 0
12:43:40.674361 IP student.51008 > 40.79.153.12.ms-sql-s: Flags [S], seq 3629156152, win 29200, options [mss 1460,sackOK,TS val 2330985419 ecr 0,nop,wscale 7], length 0
12:43:41.521620 IP student.51012 > 40.79.153.12.ms-sql-s: Flags [S], seq 384691879, win 29200, options [mss 1460,sackOK,TS val 2330986266 ecr 0,nop,wscale 7], length 0
12:43:42.530322 IP student.51012 > 40.79.153.12.ms-sql-s: Flags [S], seq 384691879, win 29200, options [mss 1460,sackOK,TS val 2330987275 ecr 0,nop,wscale 7], length 0
12:43:44.578290 IP student.51012 > 40.79.153.12.ms-sql-s: Flags [S], seq 384691879, win 29200, options [mss 1460,sackOK,TS val 2330989323 ecr 0,nop,wscale 7], length 0
12:43:48.610335 IP student.51012 > 40.79.153.12.ms-sql-s: Flags [S], seq 384691879, win 29200, options [mss 1460,sackOK,TS val 2330993355 ecr 0,nop,wscale 7], length 0
12:43:49.431704 IP student.44964 > 40.79.153.12.ms-sql-s: Flags [S], seq 824660250, win 29200, options [mss 1460,sackOK,TS val 2330994176 ecr 0,nop,wscale 7], length 0
12:43:50.466320 IP student.44964 > 40.79.153.12.ms-sql-s: Flags [S], seq 824660250, win 29200, options [mss 1460,sackOK,TS val 2330995211 ecr 0,nop,wscale 7], length 0
```

### tcpdump on the local laptop

Same story, I need to get the network interface name. `ifconfig -a` shows a lot more stuff, but I believe it's this one.

```bash
en7: flags=8963<UP,BROADCAST,SMART,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
	options=404<VLAN_MTU,CHANNEL_IO>
	ether f8:e4:3b:59:02:a0
	inet6 fe80::886:47f3:c373:4803%en7 prefixlen 64 secured scopeid 0x16
	inet 192.168.1.160 netmask 0xffffff00 broadcast 192.168.1.255
	inet6 2600:1700:1590:2270:481:7264:b2ac:29dc prefixlen 64 autoconf secured
	inet6 2600:1700:1590:2270:3ce9:3b2:e9b2:7672 prefixlen 64 autoconf temporary
	inet6 2600:1700:1590:2270::1d prefixlen 64 dynamic
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect (1000baseT <full-duplex>)
	status: active
```

There are several other interfaces, but this one shows `status: active` and has a reasonable looking local IP address. I bet it's that one. 

Now I can run `sudo tcpdump -i en7 port 1433` and wait for output.

Run `mvn liberty:run` in a separate terminal and you'll see this output.

```bash
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on en7, link-type EN10MB (Ethernet), snapshot length 524288 bytes
16:48:30.579463 IP edwards-mbp.attlocal.net.51776 > 40.78.225.32.ms-sql-s: Flags [S], seq 1887813996, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 4041241742 ecr 0,sackOK,eol], length 0
16:48:30.610481 IP 40.78.225.32.ms-sql-s > edwards-mbp.attlocal.net.51776: Flags [S.], seq 81912948, ack 1887813997, win 65535, options [mss 1440,nop,wscale 8,nop,nop,sackOK], length 0
16:48:30.610608 IP edwards-mbp.attlocal.net.51776 > 40.78.225.32.ms-sql-s: Flags [.], ack 1, win 4096, length 0
16:48:30.633758 IP edwards-mbp.attlocal.net.51776 > 40.78.225.32.ms-sql-s: Flags [P.], seq 1:68, ack 1, win 4096, length 67
16:48:30.665583 IP 40.78.225.32.ms-sql-s > edwards-mbp.attlocal.net.51776: Flags [P.], seq 1:32, ack 68, win 2052, length 31
16:48:30.665711 IP edwards-mbp.attlocal.net.51776 > 40.78.225.32.ms-sql-s: Flags [.], ack 32, win 4095, length 0
16:48:30.766804 IP edwards-mbp.attlocal.net.51776 > 40.78.225.32.ms-sql-s: Flags [P.], seq 68:582, ack 32, win 4096, length 514
16:48:30.800942 IP 40.78.225.32.ms-sql-s > edwards-mbp.attlocal.net.51776: Flags [.], seq 32:1492, ack 582, win 2050, length 1460
16:48:30.801040 IP edwards-mbp.attlocal.net.51776 > 40.78.225.32.ms-sql-s: Flags [.], ack 1492, win 4073, length 0
16:48:30.801767 IP 40.78.225.32.ms-sql-s > edwards-mbp.attlocal.net.51776: Flags [.], seq 1492:2952, ack 582, win 2050, length 1460
16:48:30.801795 IP edwards-mbp.attlocal.net.51776 > 40.78.225.32.ms-sql-s: Flags [.], ack 2952, win 4073, length 0
16:48:30.801948 IP 40.78.225.32.ms-sql-s > edwards-mbp.attlocal.net.51776: Flags [P.], seq 2952:4128, ack 582, win 2050, length 1176
```

### Comparing the two different tcpdump outputs.

Let's strip out extraneous information from the two `tcpdump` outputs for easier comparison.

**Output from the VM.** This is the one where the connection failed.

```bash
12:43:36 Flags [S], seq 2117028828, win 29200, options [mss 1460,sackOK,TS val 2330981707 ecr 0,nop,wscale 7], length 0
12:43:37 Flags [S], seq 3629156152, win 29200, options [mss 1460,sackOK,TS val 2330982307 ecr 0,nop,wscale 7], length 0
12:43:38 Flags [S], seq 3629156152, win 29200, options [mss 1460,sackOK,TS val 2330983371 ecr 0,nop,wscale 7], length 0
12:43:40 Flags [S], seq 3629156152, win 29200, options [mss 1460,sackOK,TS val 2330985419 ecr 0,nop,wscale 7], length 0
12:43:41 Flags [S], seq 384691879, win 29200, options [mss 1460,sackOK,TS val 2330986266 ecr 0,nop,wscale 7], length 0
```

**Output from the local laptop.** This is the one where the connection succeeded.

```bash
16:48:30.57 Flags [S], seq 1887813996, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 4041241742 ecr 0,sackOK,eol], length 0
16:48:30.61 Flags [S.], seq 81912948, ack 1887813997, win 65535, options [mss 1440,nop,wscale 8,nop,nop,sackOK], length 0
16:48:30.61 Flags [.], ack 1, win 4096, length 0
16:48:30.63 Flags [P.], seq 1:68, ack 1, win 4096, length 67
16:48:30.66 Flags [P.], seq 1:32, ack 68, win 2052, length 31
16:48:30.66 Flags [.], ack 32, win 4095, length 0
```

We can see right away there are differences. Apparently the app running on the VM is unable to complete the TCP handshake, while the one from the VM is not. This old mozilla doc describes the TCP handshake well [TCP handshake](https://developer.mozilla.org/en-US/docs/Glossary/TCP_handshake).

In the `tcpdump` output, there is a line for each TCP packet.  In the first three lines, we can see the three packets for the TCP handshake.

In the local laptop output:

1. At `16:48:30.57` we see the `SYN` packet going from the VM to the Azure SQL Database.
2. At `16:48:30.61` we see the reply `SYN-ACK` packet coming back from the Azure SQL Database.
3. At `16:48:30.61` we see the `ACK` packet going from the VM to the Azure SQL Database.

In the VM output we just see unanswered `SYN` packets.

If there is no firewall rule, and the `tcpdump` output shows unanswered TCP handshake packets, the most likely cause is some other network problem outside of the VM.
