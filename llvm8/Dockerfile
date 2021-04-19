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
    yum -y install gcc gcc-c++ wget openssl-devel && \
    wget http://mirror.linux-ia64.org/gnu/gcc/releases/gcc-9.3.0/gcc-9.3.0.tar.gz && \
    tar xf gcc-9.3.0.tar.gz && \
    rm gcc-9.3.0.tar.gz && \
    cd gcc-9.3.0 && \
    ./contrib/download_prerequisites && \
    mkdir build && cd build && \
    ../configure --enable-languages=c,c++ --prefix=/usr/local/ --enable-bootstrap --disable-multilib && \
    make -j`nproc` && \
    make -j`nproc` install && \
    cd /home/${USER} && rm -r gcc-9.3.0

RUN cp /usr/local/lib64/libstdc++.so.6.0.28 /usr/lib64 && \
    cd /usr/lib64 && \
    mv libstdc++.so.6 libstdc++.so.6.bak && \
    ln -s libstdc++.so.6.0.28 libstdc++.so.6

RUN cd /home/${USER} && \
    wget https://github.com/Kitware/CMake/releases/download/v3.20.1/cmake-3.20.1.tar.gz && \
    tar xf cmake-3.20.1.tar.gz && \
    rm cmake-3.20.1.tar.gz && \
    cd cmake-3.20.1 && \
    mkdir build && \
    cd build && \
    ../configure --prefix=/usr/local/ && \
    make -j`nproc` && \
    make -j`nproc` install && \
    cd /home/${USER} && rm -r cmake-3.20.1

RUN cd /home/${USER} && \
    wget https://www.python.org/ftp/python/3.9.4/Python-3.9.4.tgz && \
    tar xf Python-3.9.4.tgz && \
    rm Python-3.9.4.tgz && \
    cd Python-3.9.4 && \
    mkdir build && \
    cd build && \
    ../configure --prefix=/usr/local/ && \
    make -j`nproc` && \
    make -j`nproc` install && \
    ln -s /usr/local/bin/python3 /usr/local/bin/python && \
    cd /home/${USER} && rm -r Python-3.9.4

RUN cd /home/${USER} && \
    wget https://github.com/ninja-build/ninja/archive/v1.10.2.tar.gz && \
    tar xf v1.10.2.tar.gz && \
    rm v1.10.2.tar.gz && \
    cd ninja-1.10.2 && \
    python configure.py --bootstrap && \
    mv ninja /usr/local/bin/ && \
    cd /home/${USER} && rm -r ninja-1.10.2

RUN cd /home/${USER}/ && \
    wget https://github.com/llvm/llvm-project/archive/refs/tags/llvmorg-8.0.1.tar.gz && \
    tar xf llvmorg-8.0.1.tar.gz && \
    rm llvmorg-8.0.1.tar.gz && \
    cd llvm-project-llvmorg-8.0.1/llvm && \
    mkdir build && \
    cd build && \
    cmake .. -DLLVM_TARGETS_TO_BUILD="X86" -DLLVM_ENABLE_PROJECTS="clang;lld" -DCMAKE_BUILD_TYPE=Release -G Ninja -DCMAKE_INSTALL_PREFIX=/usr/local/ -DCMAKE_C_COMPILER=/usr/local/bin/gcc -DCMAKE_CXX_COMPILER=/usr/local/bin/g++ -DMAKE_CXX_FLAGS="-std=c++03" && \
    ninja all && ninja install && \
    cd /home/${USER} && rm -r llvm-project-llvmorg-8.0.1

USER ${USER}
WORKDIR /home/${USER}