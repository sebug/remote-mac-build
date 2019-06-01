# Remote Mac Build
Remember [tpgl](https://github.com/sebug/tpgl)? I want to be able to remotely
build (and optionally test it). Normally, I would just set up a Xamarin Mac
build agent, but let's assume we don't have the necessary CI help.

I'm starting with a connection to a MacStadium Mac Mini and try to install
all prerequisites in an automatic way (so XCode and Xamarin).

I'm learning as I go along, so if you come here early there may be better
solutions down the road.

## Connection parameters
Just so that I have them somewhere around, let's jot down the IP address, in
my shell startup file:

export BUILD_MAC_IP=...

Right out of the gate that seems fishy and I'm pondering going down the
virtualized route and spinning them up with Terraform etc. But let's put that
on the shelf for later.

I have received a support ticket containing the credentials, first thing I
do is put my ssh key on the remote machine. I log in once normally just to
confirm everything is in place

ssh administrator@$BUILD_MAC_IP

And now that that is done I can copy over my key.

ssh-copy-id -i ~/.ssh/id_rsa.pub administrator@$BUILD_MAC_IP
