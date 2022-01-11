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

## Starting proxy

To start mitm proxy just open a new terminal and type

```console
$ mitmweb # For web version
$ mitmproxy # For terminal version
```

## Installing Mitm Proxy Certificate

Now using mitm proxy you can reach and intercept all HTTP domains but when you try to surf on an HTTPS
domain it will warn you that it's not secure to proceed.

To avoid this problem you have to simply downloading Mitm Certificate going on `http://mitm.it`.

Then select your operating system, download the certificate and install it.

This way you can also reach HTTPS domains without getting that warning.

## Populate SSLKEYLOGFILE

To populate SSLKEYLOGFILE simply navigate through different pages using SSL / HTTPS such as `https://www.google.it` so that SESSION KEYS
will be created inside of SSLKEYLOGFILE.

## Configure Wireshark

After configuring the proxy we need to configure wireshark.

First of all open wireshark and go to: Edit -> Preferences -> Protocols -> TLS -> (Pre)-Master-Secret log filename
and select the target SSLKEYLOGFILE.

Now just start a new Wireshark scan on the target network-adapter and see all your requests (HTTP/HTTPS etc.)
being logged on your screen.

## Android App Sniffing
