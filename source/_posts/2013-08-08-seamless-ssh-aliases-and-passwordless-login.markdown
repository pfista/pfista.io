---
layout: post
title: "Seamless SSH: Aliases and Passwordless login"
date: 2013-08-08 17:45
comments: true
categories: code
---


Much of my development environment involves using ssh and scp on a daily basis.
There are a few easy settings that allow for a much easier way of connecting to
other servers.

### SSH Aliases
Normally, you can connect to another computer by using a command like this:

```
$ ssh username@hostname
username@hostname's password:
```

Two things can be optimized here. First, we can create an alias for our username
and hostname. For example, we could create an alias "home" that represents our 
home computer ip address and login name. This will allow us to simply type in:

```
$ ssh home
username@hostname's password:
```

This can become very useful when you have to connect to random ip addresses that
are hard to remember.  It's very easy to set up:

First, we need to get to the directory where ssh settings are stored:

```
$ cd ~/.ssh/
```

If there isn't already a ```config``` file, then go ahead and create one. To add
our alias add this to the config file:

```
Hostname home
    Hostname 10.0.0.1
    User myusername
    Port 22
```

The Port setting is optional but comes in handy if you use different ports other
than the default for ssh (22).

### SSH Password-less login

So now we have an easy way to ssh to other machines without having to type in 
pesky ip addresses or cumbersome hostnames. But, typing in a password can still 
be time consuming if you are constantly logging in and out of servers.

Thus, the passwordless logon.  For starters we must genearte asymmetric keys. I
won't enlighten you with the intricacies of public key cryptography but if you
are interested [wikipedia](https://en.wikipedia.org/wiki/Public-key_cryptography)
will always suffice.

First you will need to generate a public and private key pair.  If you already
have one then you can skip ahead to Set up login.

##### Generate the keys

First run ```ssh-keygen``` to generate the keys from the client machine. By 
default the keypair will be saved as ``~/.ssh/id_rsa`` and the public key as 
``~/.ssh/id_rsa.pub``, but you can specify a different name if you'd like.

Next it will ask you for a passphrase.  Now I know I said this is a passwordless
login.  You don't have to store a password for your private key, but if you
don't that means that anyone who gains access to your computer, either
physically or remotely, can then compromise your keys.  In effect, they could log
in to any computer that you have authorized with your keys. 

That being said, I recommend you create a password for your keypair. If you are
using OSX you can store this password in your keychain and you will only have to
enter it ocasionally.

Now you must store the client's public key on the server you want to connect to.

##### Set up auto login

Run the following command to copy the client's public key over to the server's
authorized_keys file.

{% codeblock lang:bash %}
cat ~/.ssh/id_rsa.pub | ssh home 'cat >> ~/.ssh/authorized_keys'
{% endcodeblock %}

You will have to enter your password one more time for this command to complete.

After the public key has been appended to the server's ``authorized_keys`` list you
should now be able to ssh into it without a password!

Combine that with an alias and all you have to type is ``` ssh home ```

Voila.

