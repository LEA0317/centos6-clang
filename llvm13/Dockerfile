FROM centos:centos6

LABEL maintainer "Toshihiro KONDA <kontoshi0317@gmail.com>"

ARG USER
ARG PASSWD

RUN sed -i -e "s/^mirrorlist=http:\/\/mirrorlist.centos.org/#mirrorlist=http:\/\/mirrorlist.centos.org/g" /etc/yum.repos.d/CentOS-Base.repo && \
    sed -i -e "s/^#baseurl=http:\/\/mirror.centos.org/baseurl=http:\/\/vault.centos.org/g" /etc/yum.repos.d/CentOS-Base.repo

RUN yum clean all && \
    yum -y update && \
    yum -y install sudo && \
    useradd -m ${USER} && \
    echo "${USER}:${PASSWD}" | chpasswd && \
    echo "${USER} ALL=(ALL) ALL" >> /etc/sudoers

RUN cd /home/${USER} && \
    yum -y update && \
    yum -y install gcc gcc-c++ wget openssl-devel texinfo xz && \
    wget http://mirror.linux-ia64.org/gnu/gcc/releases/gcc-10.3.0/gcc-10.3.0.tar.gz && \
    tar xf gcc-10.3.0.tar.gz && \
    rm gcc-10.3.0.tar.gz && \
    cd gcc-10.3.0 && \
    ./contrib/download_prerequisites && \
    mkdir build && cd build && \
    ../configure --enable-languages=c,c++ --prefix=/usr/local/ --enable-bootstrap --disable-multilib && \
    make -j`nproc` && \
    make -j`nproc` install && \
    cd /home/${USER} && rm -r gcc-10.3.0

RUN cp /usr/local/lib64/libstdc++.so.6.0.28 /usr/lib64 && \
    cd /usr/lib64 && \
    mv libstdc++.so.6 libstdc++.so.6.bak && \
    ln -s libstdc++.so.6.0.28 libstdc++.so.6

RUN cd /home/${USER} && \
    wget https://github.com/Kitware/CMake/releases/download/v3.21.3/cmake-3.21.3.tar.gz && \
    tar xf cmake-3.21.3.tar.gz && \
    rm cmake-3.21.3.tar.gz && \
    cd cmake-3.21.3 && \
    mkdir build && \
    cd build && \
    ../configure --prefix=/usr/local/ && \
    make -j`nproc` && \
    make -j`nproc` install && \
    cd /home/${USER} && rm -r cmake-3.21.3

RUN cd /home/${USER} && \
    wget https://www.python.org/ftp/python/3.9.7/Python-3.9.7.tgz && \
    tar xf Python-3.9.7.tgz && \
    rm Python-3.9.7.tgz && \
    cd Python-3.9.7 && \
    mkdir build && \
    cd build && \
    ../configure --prefix=/usr/local/ && \
    make -j`nproc` && \
    make -j`nproc` install && \
    ln -s /usr/local/bin/python3 /usr/local/bin/python && \
    cd /home/${USER} && rm -r Python-3.9.7

RUN cd /home/${USER} && \
    wget https://github.com/ninja-build/ninja/archive/v1.10.2.tar.gz && \
    tar xf v1.10.2.tar.gz && \
    rm v1.10.2.tar.gz && \
    cd ninja-1.10.2 && \
    python configure.py --bootstrap && \
    mv ninja /usr/local/bin/ && \
    cd /home/${USER} && rm -r ninja-1.10.2

# FIXME
RUN cd /home/${USER} && \
    wget --no-check-certificate https://ftp.gnu.org/gnu/binutils/binutils-2.36.1.tar.xz && \
    xz -dc binutils-2.36.1.tar.xz | tar xf - && \
    rm binutils-2.36.1.tar.xz && \
    cd binutils-2.36.1 && \
    mkdir build && cd build && \
    ../configure && \
    make -j `nproc` && make -j `nproc` install && \
    cd /home/${USER} && rm -r binutils-2.36.1

RUN cd /home/${USER}/ && \
    wget https://github.com/llvm/llvm-project/archive/refs/tags/llvmorg-13.0.0.tar.gz && \
    tar xf llvmorg-13.0.0.tar.gz && \
    rm llvmorg-13.0.0.tar.gz && \
    cd llvm-project-llvmorg-13.0.0/llvm && \
    mkdir build && \
    cd build && \
    cmake .. -DLLVM_TARGETS_TO_BUILD="X86" -DLLVM_ENABLE_PROJECTS="clang;lld" -DCMAKE_BUILD_TYPE=Release -G Ninja -DCMAKE_INSTALL_PREFIX=/usr/local/ -DCMAKE_C_COMPILER=/usr/local/bin/gcc -DCMAKE_CXX_COMPILER=/usr/local/bin/g++ && \
    ninja all && ninja install && \
    cd /home/${USER} && rm -r llvm-project-llvmorg-13.0.0

USER ${USER}
WORKDIR /home/${USER}