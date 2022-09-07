---
layout: single
title: '2017-08-05, ssh things'

---

# SSH
I use ssh all day all the time.  But usually it is pretty standard login, shared keys and move into my tmux session.  Other times its used a saved command to open a tunnel and doing some port forwards.  Today and the last couple days I have tarted to fill in some of the bits and pieces to really configure ssh.   That and I want to use ansible more to make my life easier, which of course leverages ssh quite a bit to get the job done.

# SSH config
## Tunnels!
I have this really long command string that I was pasting into a shell.   IT was a hassle but I had it wired up to alfred so it was not that big of a deal.   I have a feeling a number of commands that I had wired up to alfred are also going to become ansible playbook or tasks in the near future.  However that is a topic for later. But lets take my ssh tunnel command here `ssh -L 9906:10.0.0.1:3306 -L 9907:10.0.0.1:3307 -L 9909:10.0.0.4:3309 coolio@database.example.com -p 4444` and with the config it gets truncated down to `ssh tunnel-face` OR go a step further and connect it just for the ports with -N, and backgrounded with -f.

-N Do not execute a remote command. This is useful for just forwarding ports.

-f Requests ssh to go to background just before command execution.


 `ssh -f -N tunnel-face`

`Host tunnel-face
    HostName database.example.com
    Port 4444
    IdentityFile ~/.ssh/coolio.example.key
    LocalForward 9906 10.0.0.1:3306
    LocalForward 9907 10.0.0.1:3307
    LocalForward 9909 10.0.0.4:3309
    User coolio
`
[simplify-your-life-with-an-ssh-config](http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/ "simplify your life with an ssh config")

## Multiplexing
Why have one when many are better.  Next I want to speed up my ssh sessions because fast is better. That and you are going to want to make sure that the ssh mutliplex isn't stay alive too long. There is a big difference between 10 min and a persistent multiplex. This connection is also going to be used as a bastion host so it is a great case to use the multiplex. Now the ssh config is going to look a bit longer.

`Host tunnel-face
    HostName database.example.com
    Port 4444
    IdentityFile ~/.ssh/coolio.example.key
    ControlMaster auto
    ControlPersist 10m
    LocalForward 9906 10.0.0.1:3306
    LocalForward 9907 10.0.0.1:3307
    LocalForward 9909 10.0.0.4:3309
    User coolio
`

[using-ssh-multiplexing](http://blog.scottlowe.org/2015/12/11/using-ssh-multiplexing/
"using-ssh-multiplexing")

# Bastion 
noun: Fortification. a projecting portion of a rampart or fortification.  I use this host for 2 different kinds of access.  The first is a pivot box with a tmux and active sessions, making my life a little lazier because i dont need to fire up all the connections all the time.  However a different way to do this is to use it like a bastion server and spool up those connections locally and then tunnel them through the host.  Different use cases for different tasks. Either way it allows all my ssh connection through that single port and therefore I have less open ports on the firewall.  Less open ports is better.   The tricky part is the reuse of bastion entry in the config and then linking the right keys to the right locations.  Much like object oriented programming but from a configuration standpoint.
My config now becomes this.
`Host bastion
    IdentityFile ~/.ssh/id_rsa

host internal-test
    IdentityFile ~/.ssh/internal_key
    ProxyCommand ssh user1@bastion -p 3322 -W 10.0.0.1:22
    User user2 `
I can connect to the internal host through the bastion host like this `ssh internal-test`
We are going to reuse the ssh key from the bastion stanza which will then connect to the internal-test using the internal key and user2. I know that may look a little confusing but just think it through a few times and it will make sense I promise.

# The end.
Thats it that is my saturday morning journey through some bits of ssh.  Thank you to the folks who have written about this before and I tried to cite all the articles I read about it

[using ssh bastion host](http://blog.scottlowe.org/2015/11/21/using-ssh-bastion-host/ "using ssh bastion host")

[ssh multiplexing](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Multiplexing "ssh multiplexing")

# Links links
Just the links
[simplify-your-life-with-an-ssh-config](http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/ "simplify your life with an ssh config")

[using-ssh-multiplexing](http://blog.scottlowe.org/2015/12/11/using-ssh-multiplexing/
"using-ssh-multiplexing")

[ssh multiplexing](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Multiplexing "ssh multiplexing")

[using ssh bastion host](http://blog.scottlowe.org/2015/11/21/using-ssh-bastion-host/ "using ssh bastion host")

[openbsd- man ssh](https://man.openbsd.org/ssh "openbsd man ssh")
[freebsd- man ssh](https://www.freebsd.org/cgi/man.cgi?query=ssh&sektion=1 "freebsd man ssh")


