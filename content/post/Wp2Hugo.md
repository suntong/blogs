
+++
title = "Wp2Hugo"
date = "2015-12-05T23:58:01-05:00"
categories = ["Tech"]
tags = ["markdown","hugo"]
menu = ""
+++


Wp2Hugo converts wordpress's markdown meta data format into Hugo's.

<!--more-->

E.g., for an input of wordpress's markdown like this,

    # Dbab From Start To Finish

    [category Tech][tags Debian,Ubuntu,Linux,DHCP,DNS,WPAD,dnsmasq,dbab]

The output Hugo's meta data is:

    +++
    title = "Dbab From Start To Finish"
    date = "2015-12-05T23:29:19-05:00"
    categories = ["Tech"]
    tags = ["Debian","Ubuntu","Linux","DHCP","DNS","WPAD","dnsmasq","dbab"]
    +++

Usage:

    Wp2Hugo < wordpress.md > path/to/hugo.md

Source:

{{< gist suntong 0268a39d18bf3e684ff9 >}}

