# Aria2 Tools

Some tools and instructions to help supplement aria2 when used as a
download server.

## bin/aria2q

List the download queue on the local machine.

Assumes the aria2 RPC server is available at localhost:6800

## initscript/aria2c

Example Debian init script to put in ```/etc/init.d/```

It is set up for my own installation on Debian Squeeze (see instructions below).  If you're using a recent version of Ubuntu and its packaged version
of aria2 you will want to change ```/opt/aria2/bin``` for ```/usr/bin```

## initscript/aria2.conf

Example config file to put in ```/etc``` to use alongside the init script.

## Installing aria2 as download server on Debian Squeeze

The version of aria2 in the Debian Squeeze repositories is too old for these tools
and for the web client I use to handle.

The version in wheezy requires a libc update, so that's a no go too.

I just downloaded aria2 1.16.3 and built from source:

```
$ sudo apt-get build-essential aria2
$ tar xjf aria2-1.16.3.tar.bz2
$ ./configure --prefix=/opt/aria2-1.16.3
$ make
$ sudo make install
$ cd /opt
$ sudo ln -s aria2-1.16.3 aria2
$ sudo ln -s /opt/aria2/bin/aria2c /usr/local/bin/aria2c
```

Create a user to run the daemon as, and a download directory

```
$ sudo useradd --system --home-dir /var/local/aria2 download
$ sudo mkdir /var/local/aria2/down
$ sudo chown download: /var/local/aria2/down
$ sudo chmod ug=rwx,o=rx /var/local/aria2/down
```

I then copied in ```initscript/aria2c``` to ```/etc/init.d/aria2c```.

Put the following in ```/etc/aria2.conf```:

```
dir=/var/local/aria2/down
disable-ipv6=true
rpc-listen-all=true
enable-rpc=true
```

(you should set your ```dir=``` to the directory you want downloads to go in)

I then tested the init script:
```
$ sudo /etc/init.d/aria2c start
$ sudo /etc/init.d/aria2c status
$ sudo /etc/init.d/aria2c stop
$ sudo /etc/init.d/aria2c status
$ sudo /etc/init.d/aria2c start
```

Then installed it to run at startup:
```
$ sudo update-rc.d aria2c defaults
```

I then cloned a copy of ```webui-aria2``` into /opt and linked into ```/var/www``` (I already had apache web server installed):

```
$ cd /opt
$ sudo git clone git://github.com/ziahamza/webui-aria2.git
$ sudo ln -s /opt/webui-aria2 /var/www/aria2
```

Then I can browse to ```http://minerva/aria2``` from any machine on my
network to view and manipulate the download queue.

I wrote the tool ```bin/aria2q``` so I can inspect the queue from the
command line.
