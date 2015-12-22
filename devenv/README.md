Docker based Microbit dev environment
=====================================

Starting the container
----------------------

    docker run -it ncoghlan/microbit-dev

Setting up the compiler
-----------------------

These steps aren't included in the image build process due to the web service
login requirements.

    yt target bbc-microbit-classic-gcc

This will complain and present a login link for yotta.mbed.com. You will need
to create an mbed.com account (email, GitHub and BitBucket based logins are
supported) and login via a browser on the container host - the login appears
to be client IP based, as this allows the container access as well.

    yt up

Even though it's only downloading from public repos, this will want you to log
into GitHub as well. I don't know why, and by this point had decided to just go
along with ARM's obnoxiously intrusive data mining exercise.

To be continued...

Current limitations
-------------------

* Doesn't mount a host directory for working files