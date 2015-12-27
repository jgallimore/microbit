Docker based micro:bit dev environment
======================================

This container image is aimed at making it easy to get up and running with
MicroPython on the BBC micro:bit (see Nicholas Tollervey's
[write-up](http://ntoll.org/article/story-micropython-on-microbit)
for details).

It was created by Nick Coghlan as part of the
[micro:bit World Tour](https://microworldtour.github.io/about.html).

Starting the container
----------------------

First time:

    docker run -it --device=/dev/ttyACM0 --name=microbit ncoghlan/microbit-dev

Subsequent times:

    docker start -ai microbit

Caveats and other notes:

* The device passed to the container should be that of the connected micro:bit.
  "/dev/ttyACM0" appears to be the default name on both Ubuntu and Fedora, but
  it presumably may differ based on operating system and other connected devices
* The Docker daemon will complain about "error gathering device information" if
  the micro:bit isn't plugged in
* If you destroy the container and create a new one, this can confuse yotta's
  session authentication with the online components of the service. Running
  `yt logout` from inside the new container should resolve the problem
* You may want to also mount a host directory for ease of transferring files
  into and out of the container

Setting up the compiler
-----------------------

These steps aren't included in the image build process due to the web service
login requirements and the need to sign up to the BBC micro:bit NDA to access
some of the repositories prior to the official launch.

    yt target bbc-microbit-classic-gcc-nosd

This will complain and present a login link for yotta.mbed.com. You will need
to create an mbed.com account and login via a browser on the container host -
the login appears to be based on a combination of the generated session in the
URL and the client IP you use to log in, as logging in via a browser on the
host allows the build system in the container access as well.

    yt up

Some of the Microbit related repos this updates from are still under NDA, so
this will want you to log into GitHub as well. Refer to
[this issue](https://github.com/bbcmicrobit/micropython/issues/49) for more
details on the microbit-dal repository that is still subject to NDA
requirements.

(To be continued?...)

Building and installing programs
--------------------------------

The Microbit developer image defaults to launching a bash shell in a
subdirectory of the `microbitdev` user's home directory:

    /home/microbitdev/micropython

This is a clone of the https://github.com/bbcmicrobit/micropython
repository.

(To be continued...)

TODO
----

* cover building and installing the default Micropython hex file
* add screen to the image, show setting up a REPL session
* cover using ./tools/pyboard.py to run files directly
* cover using ./tools/makecombinedhex.py to have a file run on startup
  (replacing the MicroPython REPL)

Rebuilding the image
--------------------

From the devenv directory:

    docker build --tag ncoghlan/microbit-dev .

Acknowledgements
----------------

* Everyone involved in the creation of the BBC microbit project
* Nicholas Tollervey for getting the BBC microbit NDA partially lifted for
  easier community collaboration and setting up the micro:bit World Tour
  project
* Mark Schafer for the setup notes that formed the basis for this Docker image
  and README file.
