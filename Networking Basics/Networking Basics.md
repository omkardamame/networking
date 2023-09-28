## 1. Switching

`ip link`: This command shows available interfaces to link.

Consider an example where there are two hosts: A and B
Also there is a switch with IP 192.168.1.0 (so this is our network)
Here we consider that each interfaces of both hosts are connected to the switch but without adding IPs to their interfaces, we cannot ping them with each other.

We can add IPs to interfaces using following command,

```bash
ip addr add 192.168.1.10 dev eth0
```

Doing this on both sides (hosts) enables pinging between them and only enables communication within the network.

![[networking_switching.png]]

## 2. Router and Gateway

Consider we have another network with address 192.168.2.0
The systems C and D have IPs 192.168.2.10 and 192.168.2.11 resp.
So the question is how they can communicate?

![[networking_routing1.png]]

Here router comes in place.

The router enabled multiple network to able to communicate with each other, transmit data packets.

In this case, as it connects to two network, it gets assigned with two IPs.

In 1st network, we assign it an IP address - 192.168.1.1
In 2nd network, we assign it an IP address - 192.168.2.1

![[networking_routing2.png]]

Consider system C wants to send packets to system D but how can it do it?
As router is just an another device on network, there could be multiple devices on multiple networks.

That's why we configure a gateway or a route. 
Here if the network was a room then the gateway is a door to another rooms i.e. networks.
But the systems must know where that door is..

To see the existing routing, run the following command
(ubuntu)

```bash
ip route
```

(centos)

```bash
route
```

This displayed Kernel's routing table.

In this case, there is noting in routing table

![[networking_routing3.png]]

To configure a gateway on system B to reach system on network 192.168.2.0 we need to run ip route command

```bash
ip route add 192.168.2.0/24 via 192.168.1.1
```

```bash
ip route add <network_base_ip> via <gateway>
```

Running the command shows route is added.

![[networking_routing4.png]]


Remember, this has to be configured through all the systems.
For e.g., it the system C is to send a packet to system B, then we have to add a route on system C's routing table to access the network.

```bash
ip route add 192.168.1.0 via 192.168.2.1
```

Consider you want to access Google search, you'll have to add a route like before

```bash
ip route add 172.217.192.0 via 192.168.2.1
```

But if you don't know a route, simple for any network, you can say use this router as the **default gateway** by following command,

```bash
ip route add default via 192.168.2.1
```

This way, any request to any network outside of your existing network goes to this particular router.

Btw you can replace `default` with `0.0.0.0`

The `0.0.0.0` entry indicates that you don't need a gateway

## 3. IP forwarding

Consider another case where system A want to ping system C but unable to communicate as there is not routing configuration. Here as we did above we can do like this.

![[networking_ip_forwarding1.png]]

Doing this will configure the routing but we will get an empty response when pinging 192.168.2.5 from 192.168.1.5

As by default, packets are not forwarded from one interface to another in Linux as security measure. For example eth0 is connected to private network and eth1 is connected to public network, we don't want eth1 to communicate to 192.168.1.0 network without explicitly specifying it.

Here as we know both are private networks in our example, we can safely enable comms between them.

In order to do it, we need to change value 0 to 1 in following path,

```
cd /proc/sys/net/ipv4/ip_forward
```

![[networking_ip_forwarding2.png]]

This way it is NOT persistent, to make it persistent we'll need to change value to 1

![[networking_ip_forwarding3.png]]

```bash
cd /etc/sysctl.conf
```

```bash
net.ipv4.ip_forward = 1
```

