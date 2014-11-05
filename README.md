If you don't know what Weave is, read about it [here](https://github.com/zettio/weave).

_NOTE:_ This post borrows directly from [@errordeveloper's piece on the Weave Blog](http://weaveblog.com/2014/10/28/running-a-weave-network-on-coreos/) (I suggest you read that first). All I've done is saved you half an hour of rejigging the CoreOS Vagrant repository to incorporate the required changes and removed the ping example. What this gives you is a clean slate of CoreOS with Weave installed and ready to use. If you're used to using the stock CoreOS Vagrant cluster, it is now "plus Weave"!

## Installation
I've forked the CoreOS Vagrant repository and made the requisite changes. All you need to do is `git clone`, set the discovery token for Etcd and `vagrant up`, and you'll have a 3-node CoreOS cluster using Vagrant with Weave already set up and running:
```
$ git clone https://github.com/lukebond/coreos-vagrant-weave.git
$ cd coreos-vagrant-weave
$ DISCOVERY_TOKEN=`curl -s https://discovery.etcd.io/new` && perl -i -p -e "s@discovery: https://discovery.etcd.io/\w+@discovery: $DISCOVERY_TOKEN@g" user-data
$ vagrant up
```

You will need VirtualBox and Vagrant installed. I'm using Vagrant 1.6.3 and VirtualBox 4.3.12 on OS/X Yosemite.

## Usage Example
Let's use the same ping example from Ilya's blog post. Run the following sets of commands on `core-01` and `core-02`, respectively:

```
$ vagrant ssh core-01
$ docker pull busybox:latest
$ PINGER_LOCAL="10.0.1.1/24"; PINGER_REMOTE="10.0.1.2"; C=$(sudo weave run $PINGER_LOCAL --name=pinger busybox:latest ping $PINGER_REMOTE); docker attach $C
```

```
$ vagrant ssh core-02
$ docker pull busybox:latest
$ PINGER_LOCAL="10.0.1.2/24"; PINGER_REMOTE="10.0.1.1"; C=$(sudo weave run $PINGER_LOCAL --name=pinger busybox:latest ping $PINGER_REMOTE); docker attach $C
```

_e voilà_ you have two Docker containers talking to each other over the Weave network! Have fun playing with this.

You may also want to check out Weave's latest release, weaveDNS: http://weaveblog.com/2014/11/04/have-you-met-weavedns/
