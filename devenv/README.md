Docker based micro:bit dev environment
======================================

This container image is aimed at making it easy to get up and running with
MicroPython on the BBC micro:bit (see Nicholas Tollervey's
[write-up](http://ntoll.org/article/story-micropython-on-microbit)
for details).

It uses two key components to achieve this:

* Nicholas Tollervey's ["microrepl"](https://github.com/ntoll/microrepl)
  environment to interact with a MicroPython REPL installed on an attached
  micro:bit
* The `ncoghlan/microbit-dev` Docker image defined in this repo to build new
  MicroPython based firmware images for the micro:bit

The latter was created by Nick Coghlan as part of the
[micro:bit World Tour](https://microworldtour.github.io/about.html).


Using the micro:bit interactively
---------------------------------

First, you'll need to install the `microrepl` project:

    pip install --user 'pyserial<3.0' microrepl

(If you're already familiar with Python's virtual environments, you may choose
to use one of those, rather than a user level installation)

Building new images for the micro:bit
-------------------------------------

`ncoghlan/microbit-dev` is an Ubuntu 14.04 based image hosting a local copy of
the `yotta` build system for ARM's mBed platform. Built images are extracted
from the container using `docker exec`, and then copied to the micro:bit via
its USB mass storage interface.

Starting the container
~~~~~~~~~~~~~~~~~~~~~~

First time:

    docker run -it --name=microbit ncoghlan/microbit-dev

Subsequent times:

    docker start -ai microbit

The micro:bit developer image defaults to launching a bash shell in a
subdirectory of the `microbitdev` user's home directory:

    /home/microbitdev/micropython

This is a clone of the https://github.com/bbcmicrobit/micropython
repository.

Caveats and other notes:

* If you destroy the container and create a new one, this can confuse yotta's
  session authentication with the online components of the service. Running
  `yt logout` from inside the new container should resolve the problem
* You may want to also mount a host directory for ease of transferring files
  into and out of the container

Setting up the compiler
~~~~~~~~~~~~~~~~~~~~~~~

The following steps aren't included in the image build process due to the web
service login requirements and the need to sign up to the BBC micro:bit NDA to
access some of the repositories prior to the official launch.

The first step is to configure the appropriate system target in the
container:

    ~/micropython$ yt target bbc-microbit-classic-gcc-nosd

This particular target leaves out some otherwise default components, ensuring
there is enough room left on the micro:bit for the MicroPython runtime and
any application code.

The first attempt to set the target in a given container will present a login
link for yotta.mbed.com. You will need to create an mbed.com account and login
via a browser on the container host - the login appears to be based on a
combination of the generated session in the URL and the client IP you use to
log in, as logging in via a browser on the host allows the build system in
the container access as well.

The second step is to download the dependencies for the selected target and
MicroPython:

    ~/micropython$ yt up

Some of the Microbit related repos this updates from are still under NDA, so
this will want you to log into GitHub as well. Refer to
[this issue](https://github.com/bbcmicrobit/micropython/issues/49) for more
details on the microbit-dal repository that is still subject to NDA
requirements.

(To be continued?...)

Building and installing programs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

With the target set and all dependencies installed, MicroPython can be built
for the MicroBit with the following command:

    ~/micropython$ yt build



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

Clone this repo, and then run from the devenv directory:

    docker build .

The image can be made easier to use later by applying a suitable tag, like
`--tag ncoghlan/microbit-dev` (which is the tag used for the images pushed to
DockerHub).


Acknowledgements
----------------

* Everyone involved in the creation of the BBC microbit project
* Nicholas Tollervey for getting the BBC microbit NDA partially lifted for
  easier community collaboration and setting up the micro:bit World Tour
  project
* Mark Schafer for the setup notes that formed the basis for this Docker image
  and README file.
