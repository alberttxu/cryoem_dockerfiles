FROM nvidia/cuda:9.2-devel-ubuntu16.04

RUN apt update && apt install -y \
    build-essential \
    cmake \
    csh \
    curl \
    evince \
    ghostscript-x \
    git \
    libfftw3-dev \
    libtiff5-dev \
    unzip \
    xorg-dev \
 && rm -rf /var/lib/apt/lists/*

# install openmpi from source
# https://stackoverflow.com/questions/46138549/docker-openmpi-and-unexpected-end-of-proc-mounts-line
WORKDIR /usr/local/src
RUN curl https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz | tar xz
WORKDIR openmpi-4.0.0
RUN ./configure --prefix=/usr/local \
 && make -j $(grep -c processor /proc/cpuinfo) \
 && make install \
 && ldconfig

# install relion3
WORKDIR /usr/local/src
RUN git clone https://bitbucket.org/scheres/relion-3.0_beta.git
WORKDIR relion-3.0_beta/build
# cuda compute capability (without decimal point)
ARG compute_capability
RUN cmake -DCMAKE_INSTALL_PREFIX=/usr/local/relion3 -DCUDA_ARCH=$compute_capability .. \
 && make -j $(grep -c processor /proc/cpuinfo) \
 && make install \
 && echo PATH="/usr/local/relion3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin" > /etc/environment

# install ctffind4
RUN curl http://grigoriefflab.janelia.org/sites/default/files/ctffind-4.1.10-linux64.tar.gz | tar xvz -C /usr/local/

WORKDIR /usr/local/bin
# install MotionCor2_1.2.1-Cuda92
COPY --chown=root:root ./MotionCor2_1.2.1-Cuda92 /usr/local/bin

#WORKDIR /usr/local/bin
#RUN curl -OJ http://msg.ucsf.edu/MotionCor2/MotionCor2_1.2.1.zip \
 #&& unzip MotionCor2_1.2.1.zip \
 #&& rm MotionCor2_1.2.1-Cuda80 MotionCor2_1.2.1-Cuda91 MotionCor2_1.2.1.zip MotionCor2-UserManual-09-13-2018.pdf

# install Gctf
RUN curl -OJ https://www.mrc-lmb.cam.ac.uk/kzhang/Gctf/Gctf_v1.18_b2/bin/Gctf_v1.18_b2_sm61_cu9.2 \
 && chmod +x Gctf_v1.18_b2_sm61_cu9.2

# install Resmap
COPY --chown=root:root ./ResMap-1.1.4-linux64 /usr/local/bin
