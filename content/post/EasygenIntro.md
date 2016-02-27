---
title: "Easygen - Easy to use universal code/text generator"
date: "2016-01-01T22:13:12-05:00"
categories: ["Tech"]
tags: ["go","programming","easygen","CLI"]
---


<!--
# Easygen - Easy to use universal code/text generator
[category Tech][tags go,programming,easygen,CLI]
-->

The focus today is [easygen](https://github.com/suntong/easygen), a universal code/text generator that is easy to use.

<!--more-->

<a name="dt"/>
## Data transformation
[ ](https://suntong.github.io/blogs/)

I love [LaTeX](https://en.wikipedia.org/wiki/LaTeX), because I love the *concept* of it, over the [WYGIWYS](http://acronyms.thefreedictionary.com/WYGIWYS) one, because in LaTeX, you describe things in terms of *what they are*, instead of *how they look*. Thus, you can change their look to whatever you want later at a single point, without going all over the places to change each one of them. Moreover, with this *single source*, you can transform it into different presentation formats, be it `.ps`, `.pdf`, `.html`, or even `.rtf`. That's the power of *separating the data definition and data presentation*. 

Talking about *data transformation*, we all know that [XSLT](https://en.wikipedia.org/wiki/XSLT) is used for transforming data defined in XML format into other formats or even documents. It's a really good concept, but the reason that it never really takes off, I believe, is that it is really too cumbersome to use. I use [xmlstarlet](http://xmlstar.sourceforge.net/) instead to transform/convert my XML data. It is far more convenient than XSLT on the data presentation side; but still, on the data definition side, I still have to use XML, which is still not very convenient. Things will get far worse if the XML namespace is involved -- it'd be quite a struggle for me to get things right eventually. 

<a name="efdt"/>
## Easygen for data transformation
[ ](https://suntong.github.io/blogs/)

Welcome to the world of [easygen](https://github.com/suntong/easygen), in which 
data are defined far more conveniently (in [YAML](www.yaml.org/refcard.html) format); and the data transformation/presentation is far far more powerful (powered by [Go Template](https://goo.gl/dz7yih)). 

### First example

A simplest YAML example to define a list of colors:

```
Colors:
  - red
  - blue
  - white
```

A simply template to present them:

```
The colors are: {{range .Colors}}{{.}}, {{end}}.
```

I.e., no-sweat to throw in a loop, piece of cake. The result is as expected:


	$ easygen test/list0 
	The colors are: red, blue, white, .

From the same data source, you can choose either the text template or the HTML template as the presentation format: 

```bash
$ easygen test/list1
The quoted colors are: "red", "blue", "white", .

$ easygen -html test/list1
The quoted colors are: &#34;red&#34;, &#34;blue&#34;, &#34;white&#34;, .

```

As you can see, HTML specific elements ARE being taken care of, if you want them to.

### The power of Go template

Now onto the real power of Go template -- the comma after "white" and before the ending period looks ugly, let's remove it:

	$ easygen test/listfunc1 
	red, blue, white.

See? It's gone. Now, how is that done? Simple, the data source is still the same, the template is now:

	{{range $i, $color := .Colors}}{{$color}}{{if lt $i ($.Colors | len | minus1)}}, {{end}}{{end}}.

This simple template showcases Go template's power of,

- easy looping
- use of built-in functions
- use of custom defined function which is not in Go Template
- and use `if/else` for conditional output

Yet, this has only touch a tip of the iceberg of [Go Template](https://goo.gl/dz7yih). 


<a name="cg"/>
## Code generation
[ ](https://suntong.github.io/blogs/)

I'm really found of code auto-generation. I've been using a tool called GSL for that purpose for more than 10 years. If anything is *structurally repetitive*, I'll use GSL to auto-generate them for me. Commandline processing is a very good example. Take a look at the following sample, you will notice lots of repetitive things:

The help text:

```
hd2usb $ $Revision: 1.1 $

Root FS from HD to USB

Usage: hd2usb [OPTIONS]...

  -t, --max=n      max of sub-task execution level  (default=all)
  -b, --min=n      min of sub-task execution level  (default=`0')
  
      --exclude=f  use the provided exclude file instead of default

other options:
  
  -v, --verbose    be verbose, show commands run
  -n, --no-exec    no execution, only show commands to run
```

The shell commandline processing:

```bash
# -*- shell-script -*-
  
#
# shell script 'hd2usb' command line parameters processing
#
# hd2usb $ $Revision: 1.1 $
#
# Root FS from HD to USB
#
  
eval set -- `getopt \
  -o +t:b:vn --long \
    max:,min:,exclude:,verbose,no-exec \
  -- "$@"`
  
_opt_min="0"
  
while :; do
  case "$1" in
  --max|-t)            # max of sub-task execution level  (default=all)
    shift; _opt_max="$1"
    ;;
  --min|-b)            # min of sub-task execution level
    shift; _opt_min="$1"
    ;;
  # 
  --exclude)           # use the provided exclude file instead of default
    shift; _opt_exclude="$1"
    ;;
  
  # == other options
  --verbose|-v)        # be verbose, show commands run
    if [ "$_opt_verbose" ]; then _opt_verbose=`expr $_opt_verbose + 1`
    else _opt_verbose=1; fi
    VERBOSE=T
    ;;
  --no-exec|-n)        # no execution, only show commands to run
    _opt_no_exec=T
    NO_EXEC=' -n'
    ;;
  --) 
    shift; break 
    ;;
  *) 
    prog_abort "Internal getopt error ($1)!" 1
    ;;
  esac
  shift
done
  
[ "$_opt_debug" ] && {
  echo "[hd2usb] debug: _opt_max=$_opt_max"
  echo "[hd2usb] debug: _opt_min=$_opt_min"
  echo "[hd2usb] debug: _opt_exclude=$_opt_exclude"
  echo "[hd2usb] debug: _opt_verbose=$_opt_verbose"
  echo "[hd2usb] debug: _opt_no_exec=$_opt_no_exec"
}
  
#if [ "$_opt_check_failed" ]; then 
#  echo "Not all mandatory options are set."
#fi
 
# End
```

Because of those repetitive things are duplicated in two places, it'll be a maintenance nightmare to keep them both in sync, if they are not coming from a common single source. 

The [Grml](http://grml.org/)'s [grml-debootstrap(8)](https://grml.org/grml-debootstrap/)'s [command line parameter-processing](https://github.com/grml/grml-debootstrap/blob/master/cmdlineopts.clp) was initially based on my automatic generated script. The problem is that I wasn't able to open-source my code-generation script because GSL was release with a free for personal use commercial license. The newer version is now GPL, but it has loads of problems, no backward compatibility, and my script doesn't work there. Besides, the help text is automatic generated by `gengetopt`, but it's configuration file is really awkward (so I auto-generate that as well), and it has not been actively maintained for quite long. 

Thus, for years I've been looking for alternatives, and I've finally settled, with [easygen](https://github.com/suntong/easygen). Let's see how easy for it to do code generation, e.g. how it can help command line parameter handling for Go code.

<a name="efcg"/>
## Easygen for code generation
[ ](https://suntong.github.io/blogs/)

In my earlier article on [viper](https://sfxpt.wordpress.com/2015/06/25/viper-go-configuration-with-fangs/), there lists an example using `viper` and `cobra` together for configuration and command line parameter handling. If you take a closer look at the code,  from the repetitive structural code, we can extract the driving data into the following YAML definition:

```
$ cat test/commandlineCVFull.yaml
CmdMain: mainCmd
CmdPrefix: DISPATCH

Options:
  - Name: debug
    Type: Bool
    Value: false
    Usage: Turn on debugging.

  - Name: addr
    Type: String
    Value: '"localhost:5002"'
    Usage: Address of the service.

  - Name: smtp-addr
    Type: String
    Value: '"localhost:25"'
    Usage: Address of the SMTP server.

  - Name: smtp-user
    Type: String
    Value: '""'
    Usage: User for the SMTP server.

  - Name: smtp-password
    Type: String
    Value: '""'
    Usage: Password for the SMTP server.

  - Name: email-from
    Type: String
    Value: '"noreply@abc.com"'
    Usage: The from email address.

ConfigName: CommandLineCV

ConfigPath:
  - /tmp
  - .
```

Once that's done, with the help of [`commandlineCVFull.tmpl`](https://github.com/suntong/easygen/blob/master/test/commandlineCVFull.tmpl) template, auto code generation is a breeze:

```go
$ easygen test/commandlineCVFull 
func init() {

  viper.SetEnvPrefix("DISPATCH")
  viper.AutomaticEnv()

  /*

    When AutomaticEnv called, Viper will check for an environment variable any
    time a viper.Get request is made. It will apply the following rules. It
    will check for a environment variable with a name matching the key
    uppercased and prefixed with the EnvPrefix if set.

  */

  flags := mainCmd.Flags()

  flags.Bool("debug", false, "Turn on debugging.")
  viper.BindPFlag("debug", flags.Lookup("debug"))

  flags.String("addr", "localhost:5002", "Address of the service.")
  viper.BindPFlag("addr", flags.Lookup("addr"))

  flags.String("smtp-addr", "localhost:25", "Address of the SMTP server.")
  viper.BindPFlag("smtp-addr", flags.Lookup("smtp-addr"))

  flags.String("smtp-user", "", "User for the SMTP server.")
  viper.BindPFlag("smtp-user", flags.Lookup("smtp-user"))

  flags.String("smtp-password", "", "Password for the SMTP server.")
  viper.BindPFlag("smtp-password", flags.Lookup("smtp-password"))

  flags.String("email-from", "noreply@abc.com", "The from email address.")
  viper.BindPFlag("email-from", flags.Lookup("email-from"))

  // Viper supports reading from yaml, toml and/or json files. Viper can
  // search multiple paths. Paths will be searched in the order they are
  // provided. Searches stopped once Config File found.

  viper.SetConfigName("CommandLineCV") // name of config file (without extension)

  viper.AddConfigPath("/tmp")
  viper.AddConfigPath(".")

  err := viper.ReadInConfig()
  if err != nil {
    println("No config file found. Using built-in defaults.")
  }

}
```

Boom! Done! Furthermore, the code looks really like handcrafted, instead of being automatically generated.

(FTR, I used space in my Go template because the [tabs were giving me headaches when putting the results into automatic testing](https://groups.google.com/d/msg/golang-nuts/9jKexxD19Js/1hhnfmD5ckAJ). You can surely use tabs in your Go template, that's not a problem at all, I was even able to make one work for Go automatic testing. Check out the [previous example](https://github.com/suntong/easygen/blob/master/test/commandlineCV.yaml), [commandlineCV](https://github.com/suntong/easygen/blob/master/test/commandlineCV.tmpl)).

That's all for today. Happy hacking.
