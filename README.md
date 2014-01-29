linux-cheatsheets
===================

Notes by Steve Stonebraker

===================

### Networking


List of ips

    ip addr list
    ip addr list eth0
    alias ips='ip addr list | grep -o '\''inet6\? \(\([0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+\)\|[a-fA-F0-9:]\+\)'\'' | sed -e '\''s/inet6* //'\'' | sort'

Interface Info

    ifconfig -a
    ifconfig eth0

Link Status

    ip link list
    ip link list eth0
    ethtool eth0

Routing Info
    # default route
    ip route | awk '/default/{print $3}'

    ip route show || ip route list
    ip route add 192.57.66.0/24 dev eth0
    ip route get 192.57.66.42
    netstat -rn

Routing Examples


Routing Examples 2
    Open /etc/sysconfig/network and append
    GATEWAY=eth0.gateway.IP.address

    Open /etc/sysconfig/network-scripts/route-eth1 and add routing for eth1 and restart it. For example:
    202.54.1.2/29 via 202.54.2.254

    Also, set static route for /etc/sysconfig/network-scripts/route-eth0:
    10.1.2.3/8 via 10.10.38.95

Static Routes

    #The following is a sample route-eth0 file using the IP command arguments format. 
    #The default gateway is 192.168.0.1, interface eth0. 
    #The two static routes are for the 
       #10.10.10.0/24 and 
       #172.16.1.0/24 networks:
    default via 192.168.0.1 dev eth0
    10.10.10.0/24 via 192.168.0.1 dev eth0
    172.16.1.0/24 via 192.168.0.1 dev eth0

Do not add gateway entries to
/etc/sysconfig/network-scripts/ifcfg-eth1 and /etc/sysconfig/network-scripts/ifcfg-eth0
