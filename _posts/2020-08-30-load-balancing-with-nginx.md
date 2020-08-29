---
title: Load balancing with Nginx
---

Today, I carried out a simple experiment to see how load balancing
works and how I can apply it in an existing server setup.

### The stack

- A wsgi server running on port 8000. Started by a cron job. We use
  `bjoern` at the company I'm working on right now. Its probably one
  of the fastest wsgi servers for python.
- A reverse proxy server interfacing the web, listens for requests on
  port 80 (HTTP) and 443 (HTTPS) requests. We use `nginx` for this.
  `nginx` is also responsible for serving static assets separately,
  that is, static assets should not hit the wsgi server.

Although I'm saying "we", but it's just me who's managing all the
server setups and I came up with this stack instead of the old one.

### The old stack

The old stack basically ran on the python based `waitress` server
which used to listen on port 80, served all static assets through the
django server, ran as the `root` user and did not support HTTPS. This
was a very bad choice for a few obvious reasons:

- No HTTPS support.
- Ran as the `root` user. THIS MUST NEVER BE DONE UNLESS YOU KNOW WHAT
  YOU"RE GETTING INTO.
- Served static assets by the Django application itself.

Enough of the old stuffs! In our case, `nginx` just routed all the
requests to the wsgi server running on port 8000. We do not use
sockets for this.

*Side note: I tried a configuration with `systemd` once, to start the
wsgi server to create a socket, but its just too much of a hassle and
I didn't see any additional benefits in our case (pardon me, I'm not
really that knowledgable in these areas... specially with `systemd`
services and how each service depend on each other, plus socket
stuffs). Besides, starting a server on a port just seemed more
portable to me, for example, if we have the wsgi server running on
another machine.*

It was very simple to modify the `nginx` configuration file we have,
to enable load balancing. First, I created an `upstream server_pool`
branch which would serve as the pool of wsgi servers. Next, I changed
the `proxy_pass` parameter to route requests to the `server_pool`
instead of the `localhost:8000`, which was essentially the wsgi server
running on the same machine. Here's a simplified view of the config.

```nginx
upstream server_pool {
    server localhost:8000;
    server localhost:8001;
    server localhost:8002;
    server localhost:8003;
}

server {
    server_name server.com;

    # ...

    location / {
        proxy_pass http://server_pool;
    }

    # ...
}
```

Then I made sure the changes were okay, by running,

```bash
$ sudo nginx -t
```

After this, all I needed to do was increase the number of servers that
was spun up after each reboot on port 8000, 8001, 8002 and 8003. This
was just a simple cron job running a python script:

```crontab
@reboot ... python bjoern_run.py 8000 ...
@reboot ... python bjoern_run.py 8001 ...
@reboot ... python bjoern_run.py 8002 ...
@reboot ... python bjoern_run.py 8003 ...
```

Well, all of this can be simplified to a single bash script, but I
didn't bother. I was just eagerly waiting to see if the whole setup
worked or not. So, `sudo reboot` and wait for a few minuntes for the
machine to come up online. And... it worked! The server was now
running a load balancing system. Forgot to mention, but this was being
tested on a server with 4 cores (hence the choice to start four wsgi
servers) and 16 gigs of RAM. For our use case, it was more than
enough.

This configuration can be easily extended so that the wsgi servers run
on different machines, the database on another machine and the static
files on yet another machine. In that case, changing the
`localhost:8000` to an IP address should do, I have yet to try this
setup though. Maybe, I'll try it when I get some time next.

End.
