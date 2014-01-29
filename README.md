linux-cheatsheets
===================

Notes by Steve Stonebraker

===================

### Networking


List of ips

    ip addr list
    ip addr list eth0

Interface Info

    ifconfig -a
    ifconfig eth0

Link Status

    ip link list
    ip link list eth0
    ethtool eth0

Routing Info

    ip route show || ip route list
    ip route add 192.57.66.0/24 dev eth0
    ip route get 192.57.66.42
    netstat -rn

Routing Examples

    Adding routes	ip route add default via 10.0.0.1
    Add/delete IP addresses	ip addr add 10.0.0.2/24 dev eth0
    ifconfig eth0 10.0.0.2 netmask 255.255.255.0
    ip addr del 10.0.0.2/24 dev eth0
    ifconfig eth0 del 10.0.0.2
