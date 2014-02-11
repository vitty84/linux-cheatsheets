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

If parameter is unset (i.e., does not exist) or is empty, this expansion results in the value of word. If parameter is not empty, the expansion results in the value of parameter.

     [me@linuxbox ˜]$ foo=
    [me@linuxbox ˜]$ echo ${foo:-"substitute value if unset"}
    substitute value if unset
    [me@linuxbox ˜]$ echo $foo

    [me@linuxbox ˜]$ foo=bar
    [me@linuxbox ˜]$ echo ${foo:-"substitute value if unset"}
    bar
    [me@linuxbox ˜]$ echo $foo
    bar

Here is another expansion, in which we use the equal sign instead of a dash:

    ${parameter:=word}

If parameter is unset or empty, this expansion results in the value of word. In addition, the value of word is assigned to parameter. If parameter is not empty, the expansion results in the value of parameter.

    [me@linuxbox ˜]$ foo=
    [me@linuxbox ˜]$ echo ${foo:="default value if unset"}
    default value if unset
    [me@linuxbox ˜]$ echo $foo
    default value if unset
    [me@linuxbox ˜]$ foo=bar
    [me@linuxbox ˜]$ echo ${foo:="default value if unset"}
    bar
    [me@linuxbox ˜]$ echo $foo
    bar

NOTE
Positional and other special parameters cannot be assigned this way.
Here we use a question mark:

    ${parameter:?word}

If parameter is unset or empty, this expansion causes the script to exit with an error, and the contents of word are sent to standard error. If parameter is not empty, the expansion results in the value of parameter.

    [me@linuxbox ˜]$ foo=
    [me@linuxbox ˜]$ echo ${foo:?"parameter is empty"}
    bash: foo: parameter is empty
    [me@linuxbox ˜]$ echo $?
    1
    [me@linuxbox ˜]$ foo=bar
    [me@linuxbox ˜]$ echo ${foo:?"parameter is empty"}
    bar
    [me@linuxbox ˜]$ echo $?
    0

Here we use a plus sign:

    ${parameter:+word}

If parameter is unset or empty, the expansion results in nothing. If parameter is not empty, the value of word is substituted for parameter; however, the value of parameter is not changed.

    [me@linuxbox ˜]$ foo=
    [me@linuxbox ˜]$ echo ${foo:+"substitute value if set"}

    [me@linuxbox ˜]$ foo=bar
    [me@linuxbox ˜]$ echo ${foo:+"substitute value if set"}
    substitute value if set

Expansions That Return Variable Names
The shell has the ability to return the names of variables. This feature is used in some rather exotic situations.

    ${!prefix*}
    ${!prefix@}

This expansion returns the names of existing variables with names beginning with prefix. According to the bash documentation, both forms of the expansion perform identically. Here, we list all the variables in the environment with names that begin with BASH:

     [me@linuxbox ˜]$ echo ${!BASH*}
    BASH BASH_ARGC BASH_ARGV BASH_COMMAND BASH_COMPLETION BASH_COMPLETION_DIR BASH_LINENO BASH_SOURCE BASH_SUBSHELL BASH_VERSINFO BASH_VERSION

String Operations
There is a large set of expansions that can be used to operate on strings. Many of these expansions are particularly well suited for operations on pathnames. The expansion

    ${#parameter}

expands into the length of the string contained by parameter. Normally, parameter is a string; however, if parameter is either @ or *, then the expansion results in the number of positional parameters.

    [me@linuxbox ˜]$ foo="This string is long."
    [me@linuxbox ˜]$ echo "'$foo' is ${#foo} characters long."
    'This string is long.' is 20 characters long.

        ${parameter:offset}
        ${parameter:offset:length}

This expansion is used to extract a portion of the string contained in parameter. The extraction begins at offset characters from the beginning of the string and continues until the end of the string, unless the length is specified.

    [me@linuxbox ˜]$ foo="This string is long."
    [me@linuxbox ˜]$ echo ${foo:5}
    string is long.
    [me@linuxbox ˜]$ echo ${foo:5:6}
    string

If the value of offset is negative, it is taken to mean it starts from the end of the string rather than the beginning. Note that negative values must be preceded by a space to prevent confusion with the ${parameter:-word} expansion. length, if present, must not be less than 0.
If parameter is @, the result of the expansion is length positional parameters, starting at offset.

    [me@linuxbox ˜]$ foo="This string is long."
    [me@linuxbox ˜]$ echo ${foo: −5}
    long.
    [me@linuxbox ˜]$ echo ${foo: −5:2}
    lo
        ${parameter#pattern}
        ${parameter##pattern}

These expansions remove a leading portion of the string contained in parameter defined by pattern. pattern is a wildcard pattern like those used in pathname expansion. The difference in the two forms is that the # form removes the shortest match, while the ## form removes the longest match.

    [me@linuxbox ˜]$ foo=file.txt.zip
    [me@linuxbox ˜]$ echo ${foo#*.}
    txt.zip
    [me@linuxbox ˜]$ echo ${foo##*.}
    zip

        ${parameter%pattern}
        ${parameter%%pattern}

These expansions are the same as the # and ## expansions above, except they remove text from the end of the string contained in parameter rather than from the beginning.

    [me@linuxbox ˜]$ foo=file.txt.zip
    [me@linuxbox ˜]$ echo ${foo%.*}
    file.txt
    [me@linuxbox ˜]$ echo ${foo%%.*}
    file
        ${parameter/pattern/string}
        ${parameter//pattern/string}
        ${parameter/#pattern/string}
        ${parameter/%pattern/string}

This expansion performs a search and replace upon the contents of parameter. If text is found matching wildcard pattern, it is replaced with the contents of string. In the normal form, only the first occurrence of pattern is replaced. In the // form, all occurrences are replaced. The /# form requires that the match occur at the beginning of the string, and the /% form requires the match to occur at the end of the string. /string may be omitted, which causes the text matched by pattern to be deleted.

    [me@linuxbox ˜]$ foo=JPG.JPG
    [me@linuxbox ˜]$ echo ${foo/JPG/jpg}
    jpg.JPG
    [me@linuxbox ˜]$ echo ${foo//JPG/jpg}
    jpg.jpg
    [me@linuxbox ˜]$ echo ${foo/#JPG/jpg}
    jpg.JPG
    [me@linuxbox ˜]$ echo ${foo/%JPG/jpg}
    JPG.jpg

Parameter expansion is a good thing to know. The string-manipulation expansions can be used as substitutes for other common commands such as sed and cut. Expansions improve the efficiency of scripts by eliminating the use of external programs. As an example, we will modify the longest-word program discussed in the previous chapter to use the parameter expansion ${#j} in place of the command substitution $(echo $j | wc -c) and its resulting subshell, like so:

    #!/bin/bash
    
    # longest-word3 : find longest string in a file
    
    for i; do
            if [[ -r $i ]]; then
                    max_word=
                    max_len=
                    for j in $(strings $i); do
                            len=${#j}
                            if (( len > max_len )); then
                                    max_len=$len
                                    max_word=$j
                            fi
                    done
                    echo "$i: '$max_word' ($max_len characters)"
            fi
            shift
    done

Next, we will compare the efficiency of the two versions by using the time command:

     [me@linuxbox ˜]$ time longest-word2 dirlist-usr-bin.txt

    dirlist-usr-bin.txt: 'scrollkeeper-get-extended-content-list' (38 characters)
    
    real    0m3.618s
    user    0m1.544s
    sys     0m1.768s
        [me@linuxbox ˜]$ time longest-word3 dirlist-usr-bin.txt
    dirlist-usr-bin.txt: 'scrollkeeper-get-extended-content-list' (38 characters)
    
    real    0m0.060s
    user    0m0.056s
    sys     0m0.008s
    
The original version of the script takes 3.618 seconds to scan the text file, while the new version, using parameter expansion, takes only 0.06 seconds—a very significant improvement.
