Wednesday, 20th February 2019

# Editing remote files locally with help from `vim scp` and OpenSSH `ProxyJump`

Have you ever found yourself in a situation where you need to edit files on a `protected_server` that is only accessible via `ssh` through a `whitelisted_server` with a static IP?

Not an ideal workflow by any means, but allocating a pseudo-terminal with `ssh -t` to proxy through the `whitelisted_server` and edit files on the `protected_server` usually does the trick:

```bash
ssh -t me@whitelisted_server me@protected_server
```

Authenticate, fire up `vim` and you're ready to edit!

However, doing this over a slow internet connection may introduce significant network latency, and editing over `ssh` through two servers will quickly become soul-crushingly, agonisingly slow.

You start to think—wouldn't it be _great_ if I could just edit these files locally?

Most modern `vim` installations include [`netrw`](http://vimdoc.sourceforge.net/htmldoc/pi_netrw.html) file browser, which comes with a handy facility to edit and sync remote files locally, via `scp` or `sftp`. All edits are performed on a local copy of the file (usually saved somewhere in `/tmp`), and `vim` transparently handles file sync to and from the   remote server automatically. Neat!

```bash
vim scp://me@whitelisted_server//path/to/my/file.txt
```

However, there is an issue with our proxied `ssh -t` setup—we need to specify `protected_server` as the remote `scp` host, but it's only accessible through the `whitelisted_server`.

Fortunately, this is easy with a little `ssh` host configuration and `ProxyJump`. Open up `~/.ssh/config` and create a config for each host; e.g:

```bash
# ssh/config
Host whitelisted_server
  HostName 1.1.1.1
  IdentityFile ~/.ssh/whitelisted_server.pem
  User me

Host protected_server
  ProxyJump whitelisted_server # <-- specify your proxy
  HostName 1.1.1.2
  IdentityFile ~/.ssh/protected_server.pem
  User me
```

Now you can now specify the `protected_server` host alias wherever you use `ssh` and `scp` and a connection will be first established with the jump host `whitelisted_server`. Now we can easily use `vim` for remote editing again:

```bash
vim scp://protected_server//my/remote/file.txt
```

Done! :)

<small>`ProxyJump` was introduced in OpenSSH 7.3. [Check out this great answer on UNIX & Linux Stack Exchange](https://unix.stackexchange.com/questions/234903/correct-ssh-config-file-settings-to-tunnel-to-a-3rd-machine) for more usage examples and alternative methods using `ProxyCommand` for earlier versions.
