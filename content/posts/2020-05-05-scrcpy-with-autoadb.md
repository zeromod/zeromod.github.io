---
title: scrcpy with autoadb
date: "2020-05-05T00:00:00Z"
categories: [ Tools ]
tags: [ android ]
---

# Control android device over adb

## Required tools

**[ADB](https://developer.android.com/studio/command-line/adb)**

Android Debug Bridge (adb) is a versatile command-line tool that lets you communicate with a device. The adb command facilitates a variety of device actions, such as installing and debugging apps, and it provides access to a Unix shell that you can use to run a variety of commands on a device.

**[scrcpy](https://github.com/Genymobile/scrcpy)**

This application provides display and control of Android devices connected on USB (or [over TCP/IP](https://www.genymotion.com/blog/open-source-project-scrcpy-now-works-wirelessly/)). It does not require any *root* access. It works on *GNU/Linux*, *Windows* and *macOS*.

**[autoadb](https://github.com/rom1v/autoadb)**

This command-line tool allows to execute a command whenever a new device is connected to adb.

Both `scrcpy` and `autoadb` was developed by [Romain Vimont](https://github.com/rom1v).

## Current setup

I have been using `scrcpy` for a long time, to test the applications in real devices and `scrcpy` let you do it without holding the device on your hand to control it. 

The flow goes like this : 

- Connect the device
- Fire up a terminal/cmd and run `scrcpy` 
- Your device screen is now mirrored in a window and let you control the device.

### Limitations of this setup

- I need to run the command `scrcpy` every time a device is connected, also need to maintain that terminal session, which means `number of terminal sessions I need to maintain = number of devices connected`
- Simply running the command `scrcpy` works great if only a single device is connected. In case of multiple devices connected, we have to obtain the `serial` of each device by using `adb devices`, then use `scrcpy -s device_serial` for each device.

## New Setup

Later I came to know about `autoadb`, which lets you auto run any command once a device is connected via adb.

> ```shell
> autoadb printf 'Device connected\n'
> ```
>
> This will print "Device connected" whenever a new device connected via adb

`autoadb` coupled with `scrcpy` will be something like this

> ```shell
> autoadb scrcpy -s '{}'
> ```
>
> {} replaces the serial of the device detected

Now I can start controlling the device right after connecting my devices to the system

### What it solves ?

- No need to maintain multiple terminal sessions, single session is enough for either single device or multiple devices
- No need to obtain device specific `serial` via `adb devices` .
- Just connect as many device as you want and there will be a `scrcpy` window running for each device

## Bonus

Running `autoadb` as a service using [systemd](https://en.wikipedia.org/wiki/Systemd) , brings down to ultimate setup with zero efforts (no commands required, no terminal sessions to maintain).

`autoadb.service`

```shell
[Unit]
Description=autoadb

[Service]
Type=simple
Environment=ADB="full_path_to_adb" #like "/home/$USER/Android/Sdk/platform-tools/adb"
ExecStart=/usr/local/bin/autoadb scrcpy -s '{}'

[Install]
WantedBy=default.target
```

- Create the `autoadb.service` in `~/.config/systemd/user/`
- Start the service

```shell
systemctl --user start autoadb.service
```

- Auto start the service on boot

```shell
systemctl --user enable autoadb.service
```

- Check the status / logs of the service

```shell
systemctl --user status autoadb.service
```

And now whenever a device is connected via adb, you will be having a `scrcpy` window to control the device.

<iframe width="560" height="315" src="https://www.youtube.com/embed/kkQprotlWbo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Enjoy :tada:

