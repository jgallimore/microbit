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

The micro:bit developer image defaults to launching a bash shell in a
subdirectory of the `microbitdev` user's home directory:

    /home/microbitdev/micropython

This is a clone of the https://github.com/bbcmicrobit/micropython
repository.

Caveats and other notes:

* The device passed to the container should be that of the connected micro:bit.
  "/dev/ttyACM0" appears to be the default name on both Ubuntu and Fedora, but
  it presumably may differ based on operating system and other connected devices
* The Docker daemon will complain about "error gathering device information" if
  the micro:bit isn't plugged in
* If you destroy the container and create a new one, this can confuse yotta's
  session authentication with the online components of the service. Running
  `yt logout` from inside the new container should resolve the problem (the
  next `yt up` will prompt you to log in again)
* Refer to the "Building and installing programs" for how to copy files out
  of the container without needing to mount a host directory first. However,
  you may still want to mount a host directory containing source files for
  custom firmware images.


Interacting with the micro:bit
------------------------------

From within the container, run the `microrepl` command:

    microrepl

This runs Nicholas Tollervey's [microrepl](https://github.com/ntoll/microrepl/)
project to provide an interactive REPL for the MicroPython interpreter running
on the micro:bit. (If you don't currently have MicroPython, see the README in
the microrepl project for installation instructions)


Setting up the compiler
-----------------------

The following steps aren't included in the image build process due to the web
service login requirements and the need to sign up to the BBC micro:bit NDA to
access the Device Access Layer (DAL) repository prior to the official launch.

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

The container image is set up to display these two instructions every time a
new `bash` session starts (including via `docker run` and `docker start`)

Building and installing programs
--------------------------------

With the target set and all dependencies installed, MicroPython can be built
for the MicroBit with the following command:

    ~/micropython$ yt build

The various build artifacts are saved at
`build/bbc-microbit-classic-gcc-nosd/source/`.

Once a firmware image has been built inside the container, it can be copied
out to the host using `docker exec`, `cat` and a shell pipe on the host:

    docker exec microbit cat build/bbc-microbit-classic-gcc-nosd/source/microbit-micropython.hex > /run/media/ncoghlan/MICROBIT/firmware.hex

(That example copies a built MicroPython image directly to the USB mass storage
interface of the MicroBit connected to my Fedora system)

Combined images created with `tools/makecombinedhex.py` can be copied out of
the build container in the same way.

To run a Python script on the board directly without making a new firmware
image first, you can use `tools/pyboard.py`:

    ~/micropython$ python3 tools/pyboard.py /dev/ttyACM0 examples/conway.py

(The command currently needs to be run explicitly under Python 3, as there
isn't a virtual environment activated in the container by default, and
`pyserial` is only installed into the user site directory for Python 3)

Rebuilding the image
--------------------

Clone this repo, and then run from the devenv directory:

    docker build .

The image can be made easier to use later by applying a suitable tag, like
`--tag ncoghlan/microbit-dev` (which is the tag used for the images pushed to
DockerHub)


Acknowledgements
----------------

* Everyone involved in the creation of the BBC microbit project
* Damien George for creating the MicroPython project
* Nicholas Tollervey for getting the BBC microbit NDA partially lifted for
  easier community collaboration and setting up the micro:bit World Tour
  project
* Mark Schafer for the setup notes that formed the basis for this Docker image
  and README file.
