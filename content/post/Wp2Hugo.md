---
title: "Wp2Hugo"
date: "2015-12-06T09:26:47-05:00"
categories: ["Tech"]
tags: ["markdown","hugo"]
---

## Introduction

Wp2Hugo converts wordpress's markdown meta data format into Hugo's.

<!--more-->

E.g., for an input of wordpress's markdown like this,

    # Dbab From Start To Finish

    [category Tech][tags Debian,Ubuntu,Linux,DHCP,DNS,WPAD,dnsmasq,dbab]

The output Hugo's meta data is:

	---
	title: "Dbab From Start To Finish"
	date: "2015-12-06T09:57:45-05:00"
	categories: ["Tech"]
	tags: ["Debian","Ubuntu","Linux","DHCP","DNS","WPAD","dnsmasq","dbab"]
	---

## Usage

    Wp2Hugo < wordpress.md > path/to/hugo.md

## Discussion

The metadata section in Hugo is called [frontmatter](https://gohugo.io/content/front-matter/).  it *"supports a few different formats"*, and *"each with their own identifying tokens"*. What Icarus template uses is the TOML format, identified by `'+++'`, and [what hugo tutorials](https://gohugo.io/tutorials/github-pages-blog/) uses is YAML format, identified by `'---'`. 

Here is an example of meta data in TOML format:

    +++
    title = "Dbab From Start To Finish"
    date = "2015-12-05T23:29:19-05:00"
    categories = ["Tech"]
    tags = ["Debian","Ubuntu","Linux","DHCP","DNS","WPAD","dnsmasq","dbab"]
    +++

Github apparently doesn't understand the TOML format, so it just shows them as a [long line of ugly strings](https://github.com/suntong/blogs/commit/a2b128fafed6b0b3062de9ee4405705c26425bd5); yet it understands the YAML format, and [present it nicely as a table ](https://github.com/suntong/blogs/blob/master/content/post/Wp2Hugo.md).

## Source

Update: I've switched over from the original TOML format to YAML format after the initial post, so that my .md *source* will look good on Github as well. The following code is just to demonstrate the `gist` [shortcodes](http://gohugo.io/extras/shortcodes/) so it is still converting to the TOML format. You can find the [latest Wp2Hugo code here](https://github.com/suntong/lang/blob/master/lang/Go/src/text/awk/Wp2Hugo.go).

BTW, hope Hugo can add such `github` code into their [shortcodes](http://gohugo.io/extras/shortcodes/) support as well, since it will literally the same as the `gist` shortcode. 

{{< gist suntong 0268a39d18bf3e684ff9 >}}

