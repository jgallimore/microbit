FROM ubuntu:14.04
RUN apt-get -y install software-properties-common && \
    add-apt-repository -y ppa:terry.guo/gcc-arm-embedded && \
    add-apt-repository -y ppa:pmiller-opensource/ppa && \
    apt-get update && \
    apt-get -y remove binutils-arm-none-eabi gcc-arm-none-eabi && \
    apt-get -y install cmake ninja-build gcc-arm-none-eabi srecord \
                       build-essential libffi-dev libssl-dev \
                       python3-setuptools python3-pip git screen
RUN groupadd microbitdev && useradd -m -g microbitdev microbitdev
USER microbitdev
ENV LANG=C.UTF-8
RUN pip3 install --user --upgrade pip setuptools wheel
RUN pip3 install --user yotta
WORKDIR /home/microbitdev
RUN git clone https://github.com/bbcmicrobit/micropython
WORKDIR micropython
ENV PATH $PATH:/home/microbitdev/.local/bin

