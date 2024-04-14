# syntax=docker/dockerfile:1

FROM debian AS tcl-base
RUN set -o errexit -o nounset -o xtrace; \
    apt-get update; \
    apt-get install --assume-yes --no-install-recommends zlib1g; \
    rm -rf /var/lib/apt/lists/*

FROM tcl-base AS tcl-build
ARG tcl_version=8.6.14
RUN set -o errexit -o nounset -o xtrace; \
    apt-get update; \
    apt-get install --assume-yes --no-install-recommends ca-certificates curl gcc make rsync zlib1g-dev; \
    cd /usr/local/src; \
    curl --fail --location --silent https://sourceforge.net/projects/tcl/files/Tcl/${tcl_version}/tcl${tcl_version}-src.tar.gz/download \
        | tar --directory /usr/local/src --extract --file - --gunzip; \
    cd /usr/local/src/tcl${tcl_version}/unix; \
    ./configure --mandir '/usr/local/share/man'; \
    make --jobs; \
    DESTDIR=/install/dev make install; \
    rsync --archive --exclude '*.a' --exclude '*.c' --exclude /usr/local/include --exclude /usr/local/lib/pkgconfig --exclude '*.sh' --exclude /usr/local/share --remove-source-files /install/dev/ /install/run; \
    find /install -type d -empty -delete

FROM tcl-base AS tcl
COPY --from=tcl-build /install/run /

FROM tcl AS tcl-dev
COPY --from=tcl-build /install/dev /
RUN set -o errexit -o nounset -o xtrace; \
    apt-get update; \
    apt-get install --assume-yes --no-install-recommends zlib1g-dev; \
    rm -rf /var/lib/apt/lists/*
