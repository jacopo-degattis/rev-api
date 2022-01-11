# Rev-API

## Description

Step by step explanation on how to reverse engineer an API using Wireshark, MitmProxy and Frida (Android).

## Tools Needed

- [x] Wireshark - (https://www.wireshark.org/)
- [x] Frida - (https://frida.re/)
- [x] Mitm Proxy - (https://mitmproxy.org/)

## ENV Setup

First Create an evironment variable with the name 'SSLKEYLOGFILE' using the following command:

```console
$ export SSLKEYLOGFILE=<LOCATION_TO_SSLKEYLOGFILE>
```

> `N.B: LOCATION_TO_SSLKEYLOGFILE` can be a path of your choice, file will be created automatically after.

## Cofiguring proxy

After setting the ENV variable you have to set a proxy, either to your browser or to your system.

The proxy should point to host `localhost` and to port `8080` unless you have changed default configuration settings.

<!-- After setting the ENV variable you have to close all browser istances, make sure of that because it's a crucuial step.

Now start a browser instance from the exact same terminal window where you set the ENV variable.

For example on macos you can open `chromium` doing the following command:

```console
$ chromium &
```
At this point just open some SSL / HTTPS pages like for example `https://www.google.it` so that the SSLKEYLOGFILE gets populated -->

### Starting proxy

To start mitm proxy just open a new terminal and type

```console
$ mitmweb # For web version
$ mitmproxy # For terminal version
```

### Installing Mitm Proxy Certificate

Now using mitm proxy you can reach and intercept all HTTP domains but when you try to surf on an HTTPS
domain it will warn you that it's not secure to proceed.

To avoid this problem you have to simply downloading Mitm Certificate going on `http://mitm.it`.

Then select your operating system, download the certificate and install it.

This way you can also reach HTTPS domains without getting that warning.

## Populate SSLKEYLOGFILE

To populate SSLKEYLOGFILE simply navigate through different pages using SSL / HTTPS such as `https://www.google.it` so that SESSION KEYS
will be created inside of `SSLKEYLOGFILE`.

## Configure Wireshark

After configuring the proxy we need to configure wireshark.

First of all open wireshark and go to: Edit -> Preferences -> Protocols -> TLS -> (Pre)-Master-Secret log filename
and select the target SSLKEYLOGFILE.

Now just start a new Wireshark scan on the target network-adapter and see all your requests (HTTP/HTTPS etc.)
being logged on your screen.

## Android App Sniffing

In order to sniff packets of android app you need to:

- [x] Configure android emulator
- [x] Set proxy of android emulator
- [x] Install frida-server on android-emulator
- [x] Disable SSL pinning of the App

### Configure Android Emulator

The first thing to do is to configure an android emulator; the best way
is to use `Android Studio Emulators`.

When you are creating an emulator is important to choose a version that does not support
`google play store` in order to gain `root` access to the device.

### Configure Proxy

Once that the emulator is configured and you started it we have to configure proxy on it. Just go
to android emulator settings and set a proxy with host `localhost` and port `8080`.

### Install Frida - Android

Now you need to install frida-server, you can download it from the official repo: https://github.com/frida/frida/releases

After downloading frida, drag and drop the extracted content of the zip file into the emulator. Dragged file by be
available into the download folder.

At this point enter inside the emulator with a shell with the following command:

```console
$ adb root
$ adb shell
```

Then copy frida-server in `/data/local/tmp`

```
$ cd storage/emulated/0/Download
$ cp frida-server-x.y.z-android-<architecture> \
    /data/local/tmp/frida-server
```

Last thing to do is to start the frida-server doing the following:

```console
$ ./frida-server &
```

Install Objection Python Library - PC

To install `Objection` on pc we just need the following command:

```console
$ pip3 install objection
```

### Disable SSL Pinning of App

First we need to find the package of our app.

```console
$ adb shell ps # Look for your app package -> ex. com.example.org
```

Once you found the package connect to the frida process

```console
$ objection -g <package-name> explore
```

When connection has been established, disable SSL Pinning

```console
$ android sslpinning disable
```

At this point SSL should be disabled for your application and now you can
use it sniffing all requests. Requests can be seen in wireshark.
