FROM ubuntu:14.04
RUN apt-get -y install software-properties-common && \
    add-apt-repository -y ppa:terry.guo/gcc-arm-embedded && \
    add-apt-repository -y ppa:pmiller-opensource/ppa && \
    apt-get update && \
    apt-get -y remove binutils-arm-none-eabi gcc-arm-none-eabi && \
    apt-get -y install cmake ninja-build gcc-arm-none-eabi srecord \
                       build-essential libffi-dev libssl-dev \
                       python3-setuptools python3-pip git screen
# Packages that shouldn't be needed, but are useful for now...
RUN apt-get -y install vim python
# Set up a dedicated development user
RUN groupadd microbitdev && useradd -m -g microbitdev microbitdev
USER microbitdev
ENV LANG=C.UTF-8
RUN pip3 install --user --upgrade pip setuptools wheel
RUN pip3 install --user yotta
ENV PATH $PATH:/home/microbitdev/.local/bin
WORKDIR /home/microbitdev
# Print a user-modifiable prompt when launching
RUN echo '*******************************************************' > .motd && \
    echo '* Run these to ensure your environment is up to date:' >> .motd && \
    echo '* $ yt target bbc-microbit-classic-gcc-nosd' >> .motd && \
    echo '* $ yt up' >> .motd && \
    echo '*******************************************************' >> .motd && \
    echo "cat ~/.motd" >> .bashrc
# Set up a micropython checkout
RUN git clone https://github.com/bbcmicrobit/micropython
WORKDIR micropython
