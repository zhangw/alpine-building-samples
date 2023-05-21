# BUILD OPENSSH WITH MUSL

## Start the alpine container

```
docker run -v /home/vincent/Temp/alpine-built:/opt \
-v /home/vincent/Downloads/openssh-portable-V_9_3_P1:/tmp/source/openssh-portable-V_9_3_P1 \
-v /home/vincent/Downloads/openssl-3.0.7:/tmp/source/openssl-3.0.7 \
-v /home/vincent/Downloads/zlib-1.2.13:/tmp/source/zlib-1.2.13 \
-it alpine-compile-dev
```

## Build zlib

```
cd /tmp/source/zlib-1.2.13
./configure --prefix=/opt/zlib --static --64
make -j4
make test
make install
```

## Build openssl

```
cd /tmp/source/openssl-3.0.7
./Configure no-shared --prefix=/opt/openssl --openssldir=/opt/openssl-etc
make -j4
make install
```

## Build openssh

```
cd /tmp/source/openssh-portable-V_9_3_P1
export LDFLAGS="-static"
./configure --prefix=/opt/openssh --with-zlib=/opt/zlib --with-ssl-dir=/opt/openssl
make -j4
make install
/opt/openssh/sbin/sshd -t -f /opt/openssh/etc/sshd_config
```

## Check the built things

```
cd /home/vincent/Temp/alpine-built

> ls
openssh  openssl  openssl-etc  zlib
> ./openssh/bin/ssh -V
OpenSSH_9.3p1, OpenSSL 3.0.7 1 Nov 2022
> ./openssl/bin/openssl version
OpenSSL 3.0.7 1 Nov 2022 (Library: OpenSSL 3.0.7 1 Nov 2022)
```
