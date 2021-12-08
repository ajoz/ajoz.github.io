---
layout: post
date: 08-12-2021
author: Andrzej Jóźwiak
title: The pleasures of installing Android Auto DHU on Linux
tags: android-auto desktop-head-unit dhu google dhu-version-1.1 ubuntu linux
disqus: true
---

After many years of using Linux (and I am not going to be criticizing any specific distribution here) I came to the realization that installing any non trivial thing is a kind of a rite of passage. It's almost like a ceremony of going to an adulthood. The first time one modprobes psmouse module just to make the touchpad work again after upgrading the system feels like one is not a child anymore. Linux is often a test to sift the weak from the strong, a test if one still has what it takes to use it. All jokes aside, I don't mind the need to install this or that dependency but sometimes it feels like a maze that you need to navigate your way out of.

Recently I had to move to a new machine after a hardware failure in my previous latop. This meant that I needed to install Desktop Head Unit for Android Auto to work on some of my projects. Google has a nice [page](https://developer.android.com/training/cars/testing) describing the installation process but not with all the possible issues. So where should I start?

I've used the SDK Manager and downloaded version 1.1 with it. Android Auto Desktop Head Unit Emulator is easy to spot from the Android Studio UI, It took a minute to download. The package was available in the `SDK_LOCATION/extras/google/auto/` and according to Googles adivce I switched the executable bit for it with a simple `chmod +x ./desktop-head-unit`. All is fine! 

There is nothing left but to run it.

```
$ ./desktop-head-unit
./desktop-head-unit: error while loading shared libraries: libSDL2_ttf-2.0.so.0: cannot open shared object file: No such file or directory
```

Ok, so I was missing `libSDL2_ttf-2.0.so` on my Ubuntu 20.04 a quick look at the Google instruction revealed that I needed to install a few packages more for this to work:
- portaudio
- libpng
- sdl2
- sdl2_ttf

They were even kind enough to show the command that would install said packages. Running it unfortunately did not go so well.

```
$ sudo apt-get install libsdl2-2.0-0 libsdl2-ttf-2.0-0 libportaudio2 libpng12-0
(...)
Package libpng12-0 is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'libpng12-0' has no installation candidate
```

Ok, so everything else installed fine, I just need to install this `libpng12-0` and it will be fine. So `libpng12` is no longer available in the system repositories and archives, all apps should be built with the newer version `libpng16` instead. Lucky me! Ubuntu dropped `libpng12` with version `16.10` so it is definitely not available on `20.04`. 

It is not hard to find it, someone was kind enough to prepare a PPA with [it](https://www.linuxuprising.com/2018/05/fix-libpng12-0-missing-in-ubuntu-1804.html).

First adding the PPA:
```
$ sudo add-apt-repository ppa:linuxuprising/libpng12
 libpng12-0 for Ubuntu 20.10, 20.04, 19.10 or 19.04: https://www.linuxuprising.com/2018/05/fix-libpng12-0-missing-in-ubuntu-1804.html
 More info: https://launchpad.net/~linuxuprising/+archive/ubuntu/libpng12
Press [ENTER] to continue or Ctrl-c to cancel adding it.
(...)
Fetched 141 kB in 2s (66.6 kB/s)     
Reading package lists... Done
```

Then an update and installation:
```
$ sudo apt update
(...)
$ sudo apt install libpng12-0
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  libpng12-0
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/173 kB of archives.
After this operation, 290 kB of additional disk space will be used.
(Reading database ... 273706 files and directories currently installed.)
Preparing to unpack .../libpng12-0_1.2.54-1ubuntu1.1+1~ppa0~focal_amd64.deb ...
Unpacking libpng12-0:amd64 (1.2.54-1ubuntu1.1+1~ppa0~focal) ...
Setting up libpng12-0:amd64 (1.2.54-1ubuntu1.1+1~ppa0~focal) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
```

There is nothing left but to run it.

```
$ ./desktop-head-unit
./desktop-head-unit: error while loading shared libraries: libssl.so.1.0.0: cannot open shared object file: No such file or directory
```

Ok, a quick look at the list of needed packages for desktop head unit did not specify libssl. It is obvious at this point that I already had a newer version of libssl on my system, the question is where to find the needed one. Luckily enough for it was easy to [find](http://security.ubuntu.com/ubuntu/pool/main/o/openssl1.0/).

Now to download and install it.

```
$ wget http://security.ubuntu.com/ubuntu/pool/main/o/openssl1.0/libssl1.0.0_1.0.2n-1ubuntu5.7_amd64.deb
$ sudo apt install libssl1.0.0_1.0.2n-1ubuntu5.7_amd64.deb
```

There is nothing left but to run it.

```
 ./desktop-head-unit 
ALSA lib pcm_dmix.c:1089:(snd_pcm_dmix_open) unable to open slave
ALSA lib pcm.c:2642:(snd_pcm_open_noupdate) Unknown PCM cards.pcm.rear
ALSA lib pcm.c:2642:(snd_pcm_open_noupdate) Unknown PCM cards.pcm.center_lfe
ALSA lib pcm.c:2642:(snd_pcm_open_noupdate) Unknown PCM cards.pcm.side
ALSA lib pcm_route.c:869:(find_matching_chmap) Found no matching channel map
Cannot connect to server socket err = No such file or directory
Cannot connect to server request channel
jack server is not running or cannot be started
JackShmReadWritePtr::~JackShmReadWritePtr - Init not done for -1, skipping unlock
JackShmReadWritePtr::~JackShmReadWritePtr - Init not done for -1, skipping unlock
Cannot connect to server socket err = No such file or directory
Cannot connect to server request channel
jack server is not running or cannot be started
JackShmReadWritePtr::~JackShmReadWritePtr - Init not done for -1, skipping unlock
JackShmReadWritePtr::~JackShmReadWritePtr - Init not done for -1, skipping unlock
ALSA lib pcm_oss.c:377:(_snd_pcm_oss_open) Unknown field port
ALSA lib pcm_oss.c:377:(_snd_pcm_oss_open) Unknown field port
ALSA lib pcm_usb_stream.c:486:(_snd_pcm_usb_stream_open) Invalid type for card
ALSA lib pcm_usb_stream.c:486:(_snd_pcm_usb_stream_open) Invalid type for card
ALSA lib pcm_dmix.c:1089:(snd_pcm_dmix_open) unable to open slave
Cannot connect to server socket err = No such file or directory
Cannot connect to server request channel
jack server is not running or cannot be started
JackShmReadWritePtr::~JackShmReadWritePtr - Init not done for -1, skipping unlock
JackShmReadWritePtr::~JackShmReadWritePtr - Init not done for -1, skipping unlock
Connecting over ADB to localhost:5277...failed.
Failed to start Google Automotive Link.
```

Ok, finally it works. Works? but there is a glaring `failed` in the output. Yes that is true but it can be easily fixed (sic!) with a few things:
- picking a device and installing Android Auto on it
- going to Apps & Notifications > Android Auto App Info > Additinal settings in the app and clicking Version a few times to enable developer settings
- starting the head unit server from the drop down menu
- connecting the phone and running port forward `adb forward tcp:5277 tcp:5277`

and then there is nothing left but to run desktop head unit again!