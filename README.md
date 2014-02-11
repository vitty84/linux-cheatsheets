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

Group Commands and Subshells
bash allows commands to be grouped together. This can be done in one of two ways: either with a group command or with a subshell. Here are examples of the syntax of each.
Group command:

    { command1; command2; [command3; ...] }

Subshell:
 
    (command1; command2; [command3;...])

The two forms differ in that a group command surrounds its commands with braces and a subshell uses parentheses. It is important to note that, due to the way bash implements group commands, the braces must be separated from the commands by a space and the last command must be terminated with either a semicolon or a newline prior to the closing brace.

Performing Redirections

So what are group commands and subshells good for? While they have an important difference (which we will get to in a moment), they are both used to manage redirection. Let’s consider a script segment that performs redirections on multiple commands:

    ls -l > output.txt
    echo "Listing of foo.txt" >> output.txt
    cat foo.txt >> output.txt

This is pretty straightforward: three commands with their output redirected to a file named output.txt. Using a group command, we could code this as follows:

    { ls -l; echo "Listing of foo.txt"; cat foo.txt; } > output.txt

Using a subshell is similar:

    (ls -l; echo "Listing of foo.txt"; cat foo.txt) > output.txt

Using this technique, we have saved ourselves some typing, but where a group command or subshell really shines is with pipelines. When constructing a pipeline of commands, it is often useful to combine the results of several commands into a single stream. Group commands and subshells make this easy:

    { ls -l; echo "Listing of foo.txt"; cat foo.txt; } | lpr

Here we have combined the output of our three commands and piped them into the input of lpr to produce a printed report.

Process Substitution

While they look similar and can both be used to combine streams for redirection, there is an important difference between group commands and subshells. Whereas a group command executes all of its commands in the current shell, a subshell (as the name suggests) executes its commands in a child copy of the current shell. This means that the environment is copied and given to a new instance of the shell. When the subshell exits, the copy of the environment is lost, so any changes made to the subshell’s environment (including variable assignment) are lost as well. Therefore, in most cases, unless a script requires a subshell, group commands are preferable to subshells. Group commands are both faster and require less memory.

We saw an example of the subshell environment problem in Chapter 28, when we discovered that a read command in a pipeline does not work as we might intuitively expect. To recap, when we construct a pipeline like this:

    echo "foo" | read
    echo $REPLY

the content of the REPLY variable is always empty, because the read command is executed in a subshell and its copy of REPLY is destroyed when the subshell terminates.
Because commands in pipelines are always executed in subshells, any command that assigns variables will encounter this issue. Fortunately, the shell provides an exotic form of expansion called process substitution that can be used to work around this problem.

Process substitution is expressed in two ways: for processes that produce standard output:

    <(list)

or for processes that intake standard input:

    >(list)

where list is a list of commands.
To solve our problem with read, we can employ process substitution like this:

    read < <(echo "foo")
    echo $REPLY

Process substitution allows us to treat the output of a subshell as an ordinary file for purposes of redirection. In fact, since it is a form of expansion, we can examine its real value:

    [me@linuxbox ˜]$ echo <(echo "foo")
    /dev/fd/63

By using echo to view the result of the expansion, we see that the output of the subshell is being provided by a file named /dev/fd/63.

Process substitution is often used with loops containing read. Here is an example of a read loop that processes the contents of a directory listing created by a subshell:

    #!/bin/bash
    
    # pro-sub : demo of process substitution
    
    while read attr links owner group size date time filename; do
          cat <<- EOF
                Filename:   $filename
                Size:        $size
                Owner:       $owner
                Group:       $group
                Modified:   $date $time
                Links:       $links
                 Attributes: $attr
    
          EOF
    done < <(ls -l | tail -n +2)

The loop executes read for each line of a directory listing. The listing itself is produced on the final line of the script. This line redirects the output of the process substitution into the standard input of the loop. The tail command is included in the process substitution pipeline to eliminate the first line of the listing, which is not needed.
When executed, the script produces output like this:

    [me@linuxbox ˜]$ pro_sub | head -n 20
    Filename:    addresses.ldif
    Size:       14540
    Owner:       me
    Group:       me
    Modified:    2012-04-02 11:12
    Links:       1
    Attributes: -rw-r--r--
    
    Filename:   bin
    Size:       4096
    Owner:       me
    Group:       me
    Modified:    2012-07-10 07:31
    Links:       2
    Attributes: drwxr-xr-x
    
    Filename:    bookmarks.html
    Size:       394213
    Owner:       me
    Group:       me    


TEMPORARY FILES

One reason signal handlers are included in scripts is to remove temporary files that the script may create to hold intermediate results during execution. There is something of an art to naming temporary files. Traditionally, programs on Unix-like systems create their temporary files in the /tmp directory, a shared directory intended for such files. However, since the directory is shared, this poses certain security concerns, particularly for programs running with superuser privileges. Aside from the obvious step of setting proper permissions for files exposed to all users of the system, it is important to give temporary files non-predictable filenames. This avoids an exploit known as a temp race attack. One way to create a non-predictable (but still descriptive) name is to do something like this:

    tempfile=/tmp/$(basename $0).$$.$RANDOM

This will create a filename consisting of the program’s name, followed by its process ID (PID), followed by a random integer. Note, however, that the $RANDOM shell variable returns a value only in the range of 1 to 32767, which is not a very large range in computer terms, so a single instance of the variable is not sufficient to overcome a determined attacker.

A better way is to use the mktemp program (not to be confused with the mktemp standard library function) to both name and create the temporary file.

The mktemp program accepts a template as an argument that is used to build the filename. The template should include a series of X characters, which are replaced by a corresponding number of random letters and numbers. The longer the series of X characters, the longer the series of random characters. Here is an example:

    tempfile=$(mktemp /tmp/foobar.$$.XXXXXXXXXX)

This creates a temporary file and assigns its name to the variable tempfile. The X characters in the template are replaced with random letters and numbers so that the final filename (which, in this example, also includes the expanded value of the special parameter $$ to obtain the PID) might be something like

    /tmp/foobar.6593.UOZuvM6654

While the mktemp man page states that mktemp makes a temporary filename, mktemp also creates the file as well.

For scripts that are executed by regular users, it may be wise to avoid the use of the /tmp directory and create a directory for temporary files within the user’s home directory, with a line of code such as this:

    [[ -d $HOME/tmp ]] || mkdir $HOME/tmp
