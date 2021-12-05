# Source: https://github.com/qemu/qemu
# Ref: https://wiki.qemu.org/Hosts/Linux

FROM arm64v8/python:3.9-buster AS qemu-build-base

WORKDIR /   
ADD https://github.com/DF-Kyun/MyTools/blob/master/mirror/debian-buster-tsinghua-http/sources.list
/etc/apt/sources.list
RUN set -ex;\
    echo "101.6.15.130 mirrors.tuna.tsinghua.edu.cn" >> /etc/hosts;\
    ls /usr/bin/gcc* -l;\
    apt-get update;\
    apt-get install -y git;
    #apt-get upgrade -y;\
    #apt-get install -y  ninja-build=1.8.2;
    #apt-get install -y  clang-6.0;
RUN set -ex;\
    echo "180.97.125.228 gitee.com" >> /etc/hosts;\
    git clone --depth=1 --branch=v1.8.2 https://gitee.com/mirrors/ninja.git ninja;\
    cd ninja;\
    ./configure.py --bootstrap;\
    cp ninja /usr/bin/;
RUN apt show ninja-build;

#qemu-6.2.0-rc1.tar.xz文件来源https://www.qemu.org/download/
RUN set -ex;\
    wget https://download.qemu.org/qemu-6.2.0-rc1.tar.xz;\
    tar xvJf qemu-6.2.0-rc1.tar.xz;\
    mv qemu-6.2.0-rc1 qemu;\
    cd qemu;\
    echo "Install qemu version 6.2.0-rc1";

WORKDIR /qemu/build
RUN set -ex;\
    echo "begin build";\
    ../configure --target-list=i386-linux-user,x86_64-linux-user,arm-linux-user,mips64el-linux-user --disable-werror --static #--cc=/usr/lib/llvm-6.0/bin/clang;
RUN set -ex;\
    make -j8;

FROM arm64v8/busybox
WORKDIR /qemu
ENV QEMU_VERSION="6.2.0"
COPY --from=qemu-build-base /qemu/build/mips64el-linux-user/qemu-mips64el /qemu/qemu-mips64el-static
COPY --from=qemu-build-base /qemu/build/i386-linux-user/qemu-i386        /qemu/qemu-i386-static
COPY --from=qemu-build-base /qemu/build/x86_64-linux-user/qemu-x86_64        /qemu/qemu-x86_64-static
COPY --from=qemu-build-base /qemu/build/arm-linux-user/qemu-arm        /qemu/qemu-arm-static
