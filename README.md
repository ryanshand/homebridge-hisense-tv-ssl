![Homebridge-HiSense](images/header.png)

# Homebridge-Hisense-TV

[![Build and Lint](https://github.com/MrAsterisco/homebridge-hisense-tv/actions/workflows/build.yml/badge.svg?branch=master)](https://github.com/MrAsterisco/homebridge-hisense-tv/actions/workflows/build.yml)
<!-- [![npm](https://img.shields.io/npm/v/homebridge-smartglass.svg?style=flat-square)](https://www.npmjs.com/package/homebridge-smartglass)
[![npm](https://img.shields.io/npm/dt/homebridge-smartglass.svg?style=flat-square)](https://www.npmjs.com/package/homebridge-smartglass) -->


This is a plugin for Homebridge that allows you to control your RemoteNow-enabled HiSense TVs, using a custom version of the [hisensetv](https://github.com/MrAsterisco/hisensetv) tool. With this plugin, you can:

- See the status of the TV (on/off, current input).
- Turn on and off.
- List inputs (using the display name set on the TV) and switch between them.
- Control the TV volume.
- Remote control using the native iOS remote.

## Requirements:

- NodeJS 10 or later.
- Homebridge 1.3.0 or later.
- Python 3.8.
- A HiSense TV that supports the RemoteNow app ([App Store](https://apps.apple.com/us/app/remotenow/id1301866548) or [Play Store](https://play.google.com/store/apps/details?id=com.universal.remote.ms&hl=en&gl=US)).

# Installation

Search for the plugin in Homebridge:

```
homebridge-hisense-tv-remotenow
```

## Setting up the TV

First, you need to get the name of the network interface that your Homebridge machine will use to connect to the TV. On Linux, you can get a list of all the network interfaces on your machine by running:

```
ip a
```

*The name of a network interface usually looks similar to this: `ens33`.*

For this plugin to work correctly, you need to configure your TV to use a static DHCP (or configure a static reservation on your router). You also need to find your TV's MAC Address: this is usually displayed under Settings > Network Information, but it might vary based on your model.

To connect to your TV, you need to pair the machine where you're running Homebridge with your TV. This is done in the command line, by manually running the bundled `hisensetv.py` script. To do this, [find the `node_modules` folder in your system](https://docs.npmjs.com/cli/v7/configuring-npm/folders) (on Linux, it is located in `/usr/local/lib/node_modules`) and move to `homebridge-hisense-tv/bin`, then run:

```bash
python3.8 hisensetv.py <TV_IP_ADDRESS> --authorize --ifname <NETWORK_INTERFACE_NAME>
```

Your TV, if compatible, will display a PIN code: insert it in the command line and confirm. Your device is now paired with your TV and they can communicate when the TV is on. Repeat this step for all the TVs you want to use via HomeKit.

*If the command times-out after a while, make sure your TV is connected to the network and turned on. You can try to `telnet <TV_IP_ADDRESS> 36669` to make sure your Homebridge instance can reach your TV. If telnet works but this command doesn't, try to run a different command, such as `--get state` (just replace `authorize` with this in the command above): if this command succeeds, it means your TV and your machine are already paired.*

## Configure the plugin

You can use the Homebridge UI to make changes to the plugin configuration. You must set the "Network interface name" to the name you found out previously and then configure your TVs. Then, just add all the TVs you have authorized earlier:

- as ID, you can input your TV's S/N or your own identifier, as long as it's unique in your Home. You can also leave the default value, if you have just one TV. Whatever you input, will be displayed as the accessory "Serial Number" in Home.
- as name, input the display name that the Home app will suggest when adding this TV to your Home.
- as IP address, input the IP that you have assigned to your TV.
- as MAC Address, input the MAC Address of your TV (if your TV is connected both via WiFi and Ethernet, make sure to configure the interface that your TV is using).

Repeat the configuration for each TV you want to use, then restart Homebridge.

## Add the TV to Home

Once Homebridge is ready, look for a log line in the Homebridge log that looks like this one:

```
Please add [HiSense <SOME NUMBERS>] manually in Home app. Setup Code: XXX-XX-XXX.
```

Take a note of the Setup Code and open your Home app on your iPhone, iPad or Mac.

**Make sure you are connected to the same network as Homebridge and that your TV is turned on, then follow these steps:**

- Tap the "+" button in the Home app and choose "Add Accessory".
- Tap "I Don't Have a Code or Cannot Scan".
- Your device should automatically detect the TV and show it. If it doesn't, make sure your network is up. You can also try and go ahead anyway by tapping "My Accessory Isn't Shown Here".
- Input the "Setup Code" you found in the Homebridge logs.

**Done**! As always, you can repeat the steps above if you have multiple TVs to add. Please note that each TV will have a different Setup Code logged in Homebridge.

# Status
This plugin is **under active development**, but most of the features are ready to be used. There might edge cases that are not supported or not handled correctly, please see below.

## Known issues
- The input list might not be fetched correctly if the TV is turned off while adding the accessory or after restarting Homebridge. To fix this, force close your Home app and open it again.
- Switching input to "TV" might not work properly. Home will not display any error, but the next TV state refresh will bring the input back to the previous one (which is also the one displayed on the TV).
- Making changes to the TV state (turning on/off, changing input) while the Home app is opened will not trigger a live update. *This is theoretically supported by the plugin, but it seems to not work properly.*. Just switching to another app and then going back to Home will trigger a refresh.
- Some HiSense TVs have a self-signed certificate which will make any connection fail. Support for this is **planned** and it will be released in a future update, as it is already supported by the base script. If you have some free time and you'd like to contribute, please look at the Contribution section below.

# Contributions
All contributions to expand the library are welcome. Fork the repo, make the changes you want, and open a Pull Request.

If you make changes to the codebase, I am not enforcing a coding style, but I may ask you to make changes based on how the rest of the library is made.

# Credits
This plugin makes use of a modified version of the [hisensetv](https://github.com/newAM/hisensetv) Python script, originally written by [Alex](https://github.com/newAM) and distributed as open-source software [here](https://github.com/MrAsterisco/hisensetv).

The code structure and style is heavily inspired by the [homebridge-smartglass plugin](https://github.com/unknownskl/homebridge-smartglass), written by [UnknownSKL](https://github.com/unknownskl).

# License
This plugin is distributed under the MIT license. See LICENSE for details.