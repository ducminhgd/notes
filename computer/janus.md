---
type: streaming
keywors:
- webrtc
- streaming
- janus
- webrtc server
---
# Janus

Janus is a [[WebRTC]] Server

## Installation

### On Ubuntu 22.04

To make sure that you OS is updated to date

```bash
apt-get update
```

Install dependencies

```bash
apt-get install -y libmicrohttpd-dev libjansson-dev libssl-dev libsrtp2-dev libsofia-sip-ua-dev libglib2.0-dev libopus-dev libogg-dev libcurl4-openssl-dev liblua5.3-dev libconfig-dev pkg-config libtool automake autoconf git cmake
```

Install `libnice` because the available version in Ubuntu usually case problems.

    `libnice` require `meson`, `ninja` and Python 3

    ```bash
    apt-get install meson ninja-build
    ```

```bash
git clone https://gitlab.freedesktop.org/libnice/libnice
cd libnice
meson --prefix=/usr build && ninja -C build && ninja -C build install
```

For what concerns usrsctp, which is needed for Data Channels support, it is usually not available in repositories, so if you're interested in them (support is optional) you'll have to install it manually. It is a pretty easy and standard process:

```bash
cd /
git clone https://github.com/sctplab/usrsctp
cd usrsctp
./bootstrap
./configure --prefix=/usr --disable-programs --disable-inet --disable-inet6
make && make install
```

The same applies for libwebsockets, which is needed for the optional WebSockets support. If you're interested in supporting WebSockets to control Janus, as an alternative (or replacement) to the default plain HTTP REST API, you'll have to install it manually:

```bash
cd /
git clone https://libwebsockets.org/repo/libwebsockets
# If libwebsockets.org cannot be reached, please use https://github.com/warmcat/libwebsockets.git
cd libwebsockets
# If you want the stable version of libwebsockets, uncomment the next line
# git checkout v3.2-stable
mkdir build
cd build
# See https://github.com/meetecho/janus-gateway/issues/732 re: LWS_MAX_SMP
# See https://github.com/meetecho/janus-gateway/issues/2476 re: LWS_WITHOUT_EXTENSIONS
cmake -DLWS_MAX_SMP=1 -DLWS_WITHOUT_EXTENSIONS=0 -DCMAKE_INSTALL_PREFIX:PATH=/usr -DCMAKE_C_FLAGS="-fpic" ..
make && make install
```

The same applies for Eclipse Paho MQTT C client library (if needed), which is needed for the optional MQTT support. If you're interested in integrating MQTT channels as an alternative (or replacement) to HTTP and/or WebSockets to control Janus, or as a carrier of Janus Events, you can install the latest version with the following steps:

```bash
cd /
git clone https://github.com/eclipse/paho.mqtt.c.git
cd paho.mqtt.c
make && make install
```

Nanomsg support (if needed): `apt-get install libnanomsg-dev`

RabbitMQ support (if needed):

```bash
cd /
git clone https://github.com/alanxz/rabbitmq-c
cd rabbitmq-c
git submodule init
git submodule update
mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=/usr ..
make && make install
```
* *Note:* you may need to pass `--libdir=/usr/lib64` to the configure script if you're installing on a x86_64 distribution.


**Install Janus**

```bash
cd /
git clone https://github.com/meetecho/janus-gateway.git
cd janus-gateway
sh autogen.sh
./configure --prefix=/opt/janus
make
make install
```

Configure Janus:

```bash
make configs
```

### On MacOS

```bash
brew install jansson libnice openssl srtp libusrsctp libmicrohttpd libwebsockets cmake rabbitmq-c sofia-sip opus libogg curl glib libconfig pkg-config autoconf automake libtool
```