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
    #Open /etc/sysconfig/network and append
    GATEWAY=eth0.gateway.IP.address

    #Open /etc/sysconfig/network-scripts/route-eth1 and add routing for eth1 and restart it. For example:
    202.54.1.2/29 via 202.54.2.254

    #Also, set static route for /etc/sysconfig/network-scripts/route-eth0:
    10.1.2.3/8 via 10.10.38.95

    #Do not add gateway entries to
    #/etc/sysconfig/network-scripts/ifcfg-eth1 and /etc/sysconfig/network-scripts/ifcfg-eth0

Route Caching

    ip route show cache
    ip route flush cache

Static Routes - not persistant

    rhost="10.0.0.0"
    rnetmask="255.0.0.0"
    rroute="192.168.0.1"
    riface="eth2"
    
    route add -host $rroute dev $riface
    route add -net $rhost netmask $rnetmask gw $rroute
    
    echo $riface
    echo $rhost
    echo $rnetmask
    echo $rroute

Static Routes - persistant

    echo "10.0.0.0/8 via 192.168.0.1" >> /etc/sysconfig/network-scripts/route-eth2

### Bash

Basic Parameters
The simplest form of parameter expansion is reflected in the ordinary use of variables. For example, $a, when expanded, becomes whatever the variable a contains. Simple parameters may also be surrounded by braces, such as ${a}. This has no effect on the expansion, but it is required if the variable is adjacent to other text, which may confuse the shell. In this example, we attempt to create a filename by appending the string _file to the contents of the variable a.

    [me@linuxbox ˜]$ a="foo"
    [me@linuxbox ˜]$ echo "$a_file"

If we perform this sequence, the result will be nothing, because the shell will try to expand a variable named a_file rather than a. This problem can be solved by adding braces:

    [me@linuxbox ˜]$ echo "${a}_file"
    foo_file
    
We have also seen that positional parameters greater than 9 can be accessed by surrounding the number in braces. For example, to access the 11th positional parameter, we can do this: ${11}.
Expansions to Manage Empty Variables
Several parameter expansions deal with nonexistent and empty variables. These expansions are handy for handling missing positional parameters and assigning default values to parameters. Here is one such expansion:

    ${parameter:-word}
