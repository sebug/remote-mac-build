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

## Installing XCode
Let's connect again (now using the public key) and list the apps available.

	ssh administrator@$BUILD_MAC_IP
	ls /Applications

Ok, seems like an out-of-the-box install, perfect. Now I need Xcode (not just the command line tools mainly because I want to play around with it a bit more). To get it, log in to your Apple developer account, go to downloads and find the XCode version you need. Download it on your local machine and then scp it over to your mac.

	scp ~/Downloads/Xcode_10.2.1.xip administrator@$BUILD_MAC_IP:.

On the remote mac:

	xip -x Xcode_10.2.1.xip
	sudo mv Xcode.app /Applications/

After that I logged out and logged in to the mac again. XCode-select was there, and I used xcode-select --install , but it requested UI.

I downloaded the Visual Studio for Mac DMG from https://visualstudio.microsoft.com , copying that one over as well.

	cp ~/Downloads/VisualStudioForMacInstaller__1473819573.1557071077.dmg administrator@$BUILD_MAC_IP:.

Connecting again

	hdiutil attach VisualStudioForMacInstaller__1473819573.1557071077.dmg

Well, that's about as far as we can get without touching the UI. The next steps will need an SSH tunnel and VLC connection, trying that out on an unsecured network. From your local machine in a new terminal:

	ssh -L 5900:localhost:5900 -N -l administrator $BUILD_MAC_IP

Then, open vnc://localhost

The following is a clickstream of actions performed to get the build environment up and running:

1) In Spotlight, type Xcode to open it for the first time

Agree to the license agreement, typing your password

![Installing Components](https://raw.githubusercontent.com/sebug/remote-mac-build/master/images/installing_components.png "Installing XCode components")

2) In the Xcode menu (top left), choose Preferences

  Choose Accounts

  Add

  Apple ID

  Type your username and password for your Apple ID. You'll be prompted for two-factor authentication on your other devices.

3) XCode -> Quit Xcode

4) Double-click the Visual Studio for Mac Installer image attached via SSH previously

  Thank you for downloading... -> Continue

  Choose the following components:

  - .NET Core
  - Android
  - iOS

  Install

  Notice that MacStadium has a massively better connection than you do.

  You will be prompted for your password at least once.


  It will also complain about some programs (like mksdcard) not being optimised for this Mac. This means not 64 bit compatible which will become a problem very soon but doesn't worry us right now.

Click OK there.

5) Sign in with your Microsoft Account

   Enter your username and password.

   Two-factor authentication again.

6) Visual Studio -> Quit

7) Log out administrator, OK

8) Close screen sharing

9) Quit SSH tunnel (Ctr-C)


