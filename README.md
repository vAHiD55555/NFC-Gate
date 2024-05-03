NFCGate
=======

NFCGate is an Android application meant to capture, analyze, or modify NFC traffic. It can be used
as a researching tool to reverse engineer protocols or assess the security of protocols against
traffic modifications.

## Notice

This application was developed for security research purposes by students of
the [Secure Mobile Networking Lab](https://www.seemoo.tu-darmstadt.de/)
at [TU Darmstadt](https://www.tu-darmstadt.de/). Please do not use this application for malicious
purposes.

## Features

- **On-device capture**: Captures NFC traffic sent and received by other applications running on the
  device.
- **Relay**: Relays NFC traffic between two devices
  using [a server](https://github.com/nfcgate/server). One device operates as a "reader" reading an
  NFC tag, the other device emulates an NFC tag using the Host Card Emulation (HCE).
- **Replay**: Replays previously captured NFC traffic in either "reader" or "tag" mode.
- **Clone**: Clones the initial tag information (e.g. ID).
- [pcapng](https://github.com/pcapng/pcapng) export of captured NFC traffic, readable by Wireshark.

## Requirements for specific modes

- NFC support
- Android 4.4+ (API level 19+)
- [Xposed](https://repo.xposed.info/)-compatible hooking
  framework ([EdXposed](https://github.com/ElderDrivers/EdXposed), [LSPosed](https://github.com/LSPosed/LSPosed)
  with Zygisk or Riru): On-device capture, relay tag mode, replay tag mode, clone mode.
- ARMv8-A, ARMv7: Relay tag mode, replay tag mode, clone mode.
- [HCE](https://developer.android.com/guide/topics/connectivity/nfc/hce): Relay tag mode, replay tag
  mode, clone mode.

## Usage

### Building

1. Initialize submodules: `git submodule update --init`
2. Build using Android Studio or Gradle

### Operating Modes

As instructions differ per mode, each mode is described in detail in its own document
in `doc/mode/`:

- [**On-device capture**](doc/mode/OnDevice.md)
- [**Relay**](doc/mode/Relay.md)
- [**Replay**](doc/mode/Replay.md)
- [**Clone**](doc/mode/Clone.md)

### Pcapng Export

Captured traffic can be exported in or imported from the [pcapng](https://github.com/pcapng/pcapng)
file format. For example, Wireshark can be used to further analyze NFC traffic. A detailed
description of the import and export functionality is documented in [doc/pcapng.md](doc/pcapng.md).

## Compatibility

NFCGate provides an in-app status check. For further notes on compatibility see
the [compatibility document](doc/Compatibility.md).

## Known Issues and Caveats

Please consider the following issues and caveats before using the application (and especially before
filing a bug report).

### NFC Stack

When using modes, that utilize HCE, the phone has to implement
the [NFC Controller Interface (NCI)](https://nfc-forum.org/our-work/specifications-and-application-documents/specifications/nfc-controller-interface-nci-specification/)
specification. Most of the phones should implement this specification when offering HCE support.

### Confidentiality of Data Channel (relay)

To ensure confidentiality and integrity, use Transport Layer Security (TLS), which can be enabled in
NFCGate settings. You need a CA-issued or self-signed certificate. Certificates from system-trusted
CAs are trusted automatically. Self-signed certificates can be trusted by the user on first use (
TOFU).

### Compatibility with Cards (relay, replay, clone)

We can only proxy tags supported by Android. For example, Android no longer offers support for
MiFare classic chips, so these cards are not supported. When in doubt, use an application like NFC
Tag info to find out if your tag is compatible. Also, at the moment, every tag technology supported
by Android's HCE is supported (A, B, F), however NFC-B and NFC-F remain untested. NFC-A tags are the
most common tags (for example, both the MiFare DESFire and specialized chips like the ones in
electronic passports use NFC-A), but you may experience problems if you use other tags.

### Compatibility with readers (relay)

This application only works with readers which do not implement additional security measures. One
security measure which will prevent our application from working in relay mode is when the reader
checks the time it takes the card to respond (or, to use the more general case, if the reader
implements "distance bounding"). The network transmission adds a noticeable delay to any
transaction, so any secure reader will not accept our proxied replies.  
This does not affect other operating modes.

### Android NFC limitations (relay, replay)

Some features of NFC are not supported by Android and thus cannot be used with our application. We
have experienced cases where the NFC field generated by the phone was not strong enough to properly
power more advanced features of some NFC chips (e.g. cryptographic operations). Keep this in mind if
you are testing chips we have not experimented with.

### OnePlus Devices

See the [note](doc/Compatibility.md#oneplus-devices) about OnePlus devices.

## Publications and Media

This application
was [presented at the 14th USENIX Workshop on Offensive Technologies (WOOT '20)](https://www.usenix.org/conference/woot20/presentation/klee).
An [arXiv preprint can be found here](https://arxiv.org/abs/2008.03913).

An early version of this application was presented at WiSec 2015.
The [extended Abstract](https://tuprints.ulb.tu-darmstadt.de/5414/1/NFCGate%20-%20Maass%20et%20al.pdf)
and [poster](https://blog.velcommuta.de/wp-content/uploads/2015/07/NFCGate-Poster.pdf) can be found
on the [website](https://blog.velcommuta.de/publications/) of one of the authors. It was also
presented in a
brief [Lightning Talk](https://media.ccc.de/browse/conferences/camp2015/camp2015-6862-lightning_talks_day_2.html#video&t=300)
at the [Chaos Communication Camp 2015](https://events.ccc.de/camp/2015/wiki/Main_Page).

## Reference our Project

Any use of this project which results in an academic publication or other publication which includes
a bibliography should include a citation to NFCGate:

```
@inproceedings {Klee2020Nfcgate,
    author = {Steffen Klee and Alexandros Roussos and Max Maass and Matthias Hollick},
    title = {NFCGate: Opening the Door for {NFC} Security Research with a Smartphone-Based Toolkit},
    booktitle = {14th {USENIX} Workshop on Offensive Technologies ({WOOT} 20)},
    year = {2020},
    url = {https://www.usenix.org/conference/woot20/presentation/klee},
    publisher = {{USENIX} Association},
    month = aug,
}
```

The initial NFCGate paper describing the first version of NFCGate can be cited as follows:

```
@inproceedings{Maass2015Nfcgate,
  title={DEMO: NFCGate: an NFC relay application for Android},
  author={Max Maass and Uwe M{\"u}ller and Tom Schons and Daniel Wegemer and Matthias Schulz},
  booktitle={Proceedings of the 8th ACM Conference on Security \& Privacy in Wireless and Mobile Networks},
  year={2015}
}
```

## License

```
   Copyright 2015-2022 NFCGate Team

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
```

## Contact

* [Steffen Klee](https://www.seemoo.tu-darmstadt.de/team/sklee/)
* [Max Maass](https://www.seemoo.tu-darmstadt.de/team/mmaass/)

## Used Libraries

- [xHook](https://github.com/iqiyi/xHook) (Licensed under
  the [MIT License](https://opensource.org/licenses/MIT))
- [Xposed Bridge](https://github.com/rovo89/XposedBridge) (Licensed under
  the [Apache License v2.0](http://opensource.org/licenses/Apache-2.0))
- [LibNFC-NCI](https://android.googlesource.com/platform/external/libnfc-nci/) (Licensed under
  the [Apache License v2.0](http://opensource.org/licenses/Apache-2.0))
- [Protobuf](https://github.com/protocolbuffers/protobuf) (Licensed under the
  modified [BSD 3-Clause License](http://opensource.org/licenses/BSD-3-Clause))
- [Android About Page](https://github.com/medyo/android-about-page) (Licensed under
  the [MIT License](https://opensource.org/licenses/MIT))
- [Android Device Names](https://github.com/jaredrummler/AndroidDeviceNames) (Licensed under
  the [Apache License v2.0](http://opensource.org/licenses/Apache-2.0))
- [Android Support library - preference v7 bugfix](https://github.com/Gericop/Android-Support-Preference-V7-Fix) (
  Released into the public domain and partly licensed under
  the [Apache License v2.0](http://opensource.org/licenses/Apache-2.0))
- [Android Room](https://developer.android.com/topic/libraries/architecture/room) (Licensed under
  the [Apache License v2.0](http://opensource.org/licenses/Apache-2.0))
- [Android Lifecycle](https://developer.android.com/topic/libraries/architecture/lifecycle) (
  Licensed under the [Apache License v2.0](http://opensource.org/licenses/Apache-2.0))

## Credits

- [ADBI](https://github.com/crmulliner/adbi): ARM and THUMB inline hooking
