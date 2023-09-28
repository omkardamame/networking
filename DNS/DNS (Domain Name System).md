## 1. Adding hostnames in hosts file

One can always ping other system with their IP address like this,

```bash
ping 192.168.1.1
```

But you can assign a name like db

```bash
ping db
```

But this will yield an error as  `unknow host db`.

So you must let your system know that db=192.168.1.1. In order to do that, you will need to do some changes,

```bash
vim /etc/hosts
```

Here you can add your host names with their IPs.
You can always assign multiple host names to same IP.

![dns_hosts.png](https://github.com/omkardamame/networking/blob/main/DNS/dns_hosts.png)

So when using ``CURL`` or ``SSH`` you can just use their name.

```bash
ssh db
```

This system is efficient for small network with very few hosts but as hosts keep increases, you'll have to add more hosts to each and every system's host file and if IP changes for any of them, you'll have to do the same changes to all hosts which is just cumbersome.

## 2. DNS server

In this case, we create a server which only handles these changes centrally, we call it DNS server.
Now we just point all hosts to look up that server to resolve the IP addresses.

![dns_dns.png](https://github.com/omkardamame/networking/blob/main/DNS/dns_dns.png)

So in order to set DNS on each hosts, change in ``resolv.conf`` file,

```bash
vim /etc/resolv.conf
```

Change value

```
nameserver  192.168.1.100
```

We then no longer need entries in hosts file.

![[dns_changing_dns.png]](https://github.com/omkardamame/networking/blob/main/DNS/dns_changing_dns.png)

## 3. Priority for name resolution

Default priority while resolving host name is,

1. /etc/hosts file
2. /etc/resolv.conf

This 2nd will result in system querying the DNS server for name resolution.

This priority can be changed from ``nsswitch.conf`` file

```bash
vim /etc/nsswitch.conf
```

![[dns_priority.png]](https://github.com/omkardamame/networking/blob/main/DNS/dns_priority.png)

Here `files` is present in 1st which means system will look into hosts file for name resolution before going to DNS server.

## 4. Adding custom DNS

In order to have a custom DNS or just want to have access to public DNS, you can do following changes.

Go to ``resolve.conf``

```bash
vim /etc/resolv.conf
```

Add another nameserver

```
nameserver  69.69.69.69
nameserver  1.1.1.1
```

This way even if you ping `www.google.com`, you can get pong from it.

## 5. Record Types

There are basic record types such as,

1. A: It stores IPv4 to hostname
2. AAAA: It stores IPv6 to hostname
3. CNAME: Mapping one name to another name (name to name mapping)

![[dns_record_types.png]](https://github.com/omkardamame/networking/blob/main/DNS/dns_record_types.png)

## 6. nslookup

Pinging is not the good option to see hostname.

You can use ``nslookup`` to query a hostname from a DNS server.
It DOES NOT consider the entries in local hosts file.
It only queries DNS server.

![[dns_nslookup.png]](https://github.com/omkardamame/networking/blob/main/DNS/dns_nslookup.png)

## 7. dig

Also, it is an another tool to test DNS resolution.
It returns more details in similar form as it stored on the server.

![[dns_dig.png]](https://github.com/omkardamame/networking/blob/main/DNS/dns_dig.png)
