# Useful ADB + Shell Commands
- [ADB Server Basics](#ADB-Server-Basics)
- [Copy files to/from a device](#copy-files-tofrom-a-device)
- [Android package manager (pm)](#android-package-manager-pm)
    - [Install an app](#Install-an-app)
    - [Remove Apps](#remove-application-command)
 - [Activate USB Tethering](#activate-usb-tethering)
 - [Change Screen Density](#change-screen-density)
 - [VPN over tethering]()
- [Research](#Research)

## ADB Server Basics
~~~ 
adb devices // To confirm device is connected
adb kill-server // To reset ADB host
adb connect <device local ip> // To connect via wifi
~~~
## Copy files to/from a device
**To copy `from` a device:**

`adb` `pull` `<device>` `<computer>`

**To copy `to` a device:**

`adb` `push` `<computer>` `<device>`

**Example:**
~~~
adb push foo.txt /sdcard/foo.txt
adb pull /sdcard/bar.txt C:\
~~~



**Example:**
~~~
adb install "C:\com.google.android.youtube.apk"
~~~

# Android package manager (pm)

**Get list of `system apps` on the device:**
```bash
adb shell "echo 'apps:' && pm list packages -f | grep /system/app/ | sed 's/.*=/  - /'"
```
**Directories:** System apps / pre-installed-bloatware-apps are stored in ``/system/app`` with privileged apps in ``/system/priv-app``

**Formatting Regex:** `s/.*=/  - /`

## Install an app
`adb` `install` `path_to_apk`

## Remove application command
```bash
pm uninstall --user 0 app
```
**Option/switches:** 

**-k**: To keep the data and cache directories around after package removal.

**Example:**
```bash
pm uninstall -k --user 0 com.facebook.system
pm uninstall -k --user 0 com.facebook.services
pm uninstall -k --user 0 com.facebook.katana
pm uninstall -k --user 0 com.facebook.appmanager
```

## Activate USB Tethering 
**This depends on the Android version:**

~~~
service call connectivity 32 i32 1 on Ice Cream Sandwich (4.0)
service call connectivity 33 i32 1 on Jelly Bean (4.1 to 4.3)
service call connectivity 34 i32 1 on KitKat (4.4)
service call connectivity 30 i32 1 on Lollipop (5.0)
service call connectivity 31 i32 1 on Lollipop (5.1) 
service call connectivity 30 i32 1 on Marshmallow (6.0)
service call connectivity 41 i32 1 on Samsung Marshmallow (6.0)
service call connectivity 33 i32 1 on Nougat (7.0)
service call connectivity 39 i32 1 on Samsung Nougat (7.0)
~~~

**For Android 8 & above:**
~~~
service call connectivity 34 i32 1 s16 text (8.0/8.1)
service call connectivity 33 i32 1 s16 text  (9.0)
~~~

**Example (Android 8.1):**

~~~
adb shell "su -c 'service call connectivity 34 i32 1 s16 text'"
~~~

## Change Screen Density
`adb` `shell` `wm` `density` `<value>`
**Example:**
~~~
adb shell wm density 480
adb shell wm density reset // set to default density
~~~

## VPN Over Tethering
A dirty way to forward VPN over any tethered connection on a Android device:

~~~
#!/system/bin/sh

# Turn on tethering, then enable VPN, then run this script.

# Grant root access
su

# Setup iptables before forwarding VPN
iptables -t filter -F FORWARD
iptables -t nat -F POSTROUTING
iptables -t filter -I FORWARD -j ACCEPT
iptables -t nat -I POSTROUTING -j MASQUERADE

# forward VPN to wlan0 (change to rndis0 for usb tethering)
ip rule add from 192.168.43.0/24 lookup 61
ip route add default dev tun0 scope link table 61
ip route add 192.168.43.0/24 dev <interface> scope link table 61
ip route add broadcast 255.255.255.255 dev wlan0 scope link table 61

echo "Set up VPN on Wifi hotspot sucessfully"
~~~

# Research
- [Official ADB Documentation](https://developer.android.com/studio/command-line/adb)
- [Calling Android services from ADB shell](http://ktnr74.blogspot.com/2014/09/calling-android-services-from-adb-shell.html)
- [Is it possible to USB tether an android device using adb through the terminal?](https://stackoverflow.com/questions/20226924/is-it-possible-to-usb-tether-an-android-device-using-adb-through-the-terminal)
- [AdbCommands](https://gist.github.com/Pulimet/5013acf2cd5b28e55036c82c91bd56d8)


