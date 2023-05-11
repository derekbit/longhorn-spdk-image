FROM registry.suse.com/bci/bci-base:15.4

ARG ARCH=amd64

RUN zypper -n addrepo --refresh https://download.opensuse.org/repositories/system:/snappy/SLE_15/system:snappy.repo && \
    zypper -n addrepo --refresh https://download.opensuse.org/repositories/network:/utilities/SLE_15_SP4/network:utilities.repo && \
    zypper -n addrepo --refresh https://download.opensuse.org/repositories/devel:libraries:c_c++/15.4/devel:libraries:c_c++.repo && \
    zypper -n addrepo --refresh https://download.opensuse.org/repositories/devel:languages:python:Factory/15.4/devel:languages:python:Factory.repo && \
    zypper -n addrepo --refresh https://download.opensuse.org/repositories/devel:languages:python:backports/15.4/devel:languages:python:backports.repo && \
    zypper --gpg-auto-import-keys ref

RUN zypper -n install cmake kmod curl nfs-client nfs4-acl-tools fuse git gcc \
    libibverbs librdmacm1 rdma-core-devel perl-Config-General libaio-devel sg3_utils \
    iputils telnet iperf qemu-tools wget iproute2 xsltproc docbook-xsl-stylesheets \
    e2fsprogs e2fsprogs-devel xfsprogs xfsprogs-devel cifs-utils

RUN if [ ${ARCH} == "amd64" ]; then \
        zypper -n install autoconf libtool libunwind-devel gcc-c++; \
    fi

# Build SPDK
ENV SPDK_DIR /usr/src/spdk
RUN git clone https://github.com/derekbit/spdk.git ${SPDK_DIR} --recursive && \
    cd ${SPDK_DIR} && \
    git submodule update --init && \
    ./scripts/pkgdep.sh && \
    ./configure --target-arch=ivybridge --disable-tests --disable-unit-tests --disable-examples && \
    make -j$(nproc) && \
    make install && \
    rm -rf ${SPDK_DIR}


VOLUME /usr/local/bin

# Add Tini
ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-${ARCH} /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]

CMD ["longhorn"]
