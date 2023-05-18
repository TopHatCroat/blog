+++
title = "KDE Connect commands"
description = "Use your Android phone to send commands to your computer"
tags = [
  "kde connect",
  "workflow"
]
date = "2018-10-20"
+++

# KDE Connect useful commands

So I've been using KDE Connect Android application for a while now due to its
great integration KDE Plasma desktop.
It allows you to send files over, share your clipboard, sync notifications and
has other useful features like remote control using the phones screen as a
touchpad or keyboard on your computer which is greatly appreciated feature for
when you just couldn't be asked to reach for your mouse to select the next
YouTube video to play.

One feature which requires a bit of effort to get the most of it are commands.
KDE Connect gives you an option to add named shortcuts for arbitrary commands to
run on your computer from your phone!
The simplest example of this would be to have a shortcut named "Suspend" and a
command which states `systemctl suspend`.
You guessed it, it would put the computer to sleep.
So, as you can imagine, this is quite a powerful feature.

These are some of the commands that I use:

```sh
PC Suspend:
systemctl suspend
PC Lock:
qdbus org.freedesktop.ScreenSaver /ScreenSaver Lock
PC Shutdown:
systemctl poweroff

Volume Up:
qdbus org.kde.kglobalaccel /component/kmix invokeShortcut "increase_volume"
Volume Down:
qdbus org.kde.kglobalaccel /component/kmix invokeShortcut "decrease_volume"
Volume Mute:
qdbus org.kde.kglobalaccel /component/kmix invokeShortcut "mute"

Screenshot Fullscreen:
spectacle -b -f -o ~/Pictures/Screenshots/screenshot-$(date +'%y-%m-%d_%T').png
Screenshot current window:
spectacle -b -a -o ~/Pictures/Screenshots/screenshot-$(date +'%y-%m-%d_%T').png

Bluetooth On:
bluetoothctl power on
Bluetooth Off:
bluetoothctl power off
Bluetooth Connect A320:
bluetoothctl connect AB:AB:FA:2F:36:AB
Bluetooth Disconnect A320:
bluetoothctl disconnect AB:AB:FA:2F:36:AB

Brightness Up:
qdbus org.kde.Solid.PowerManagement /org/kde/Solid/PowerManagement/Actions/BrightnessControl org.kde.Solid.PowerManagement.Actions.BrightnessControl.setBrightness $(expr $(qdbus org.kde.Solid.PowerManagement /org/kde/Solid/PowerManagement/Actions/BrightnessControl org.kde.Solid.PowerManagement.Actions.BrightnessControl.brightness) + 20)
Brightness Down:
qdbus org.kde.Solid.PowerManagement /org/kde/Solid/PowerManagement/Actions/BrightnessControl org.kde.Solid.PowerManagement.Actions.BrightnessControl.setBrightness $(expr $(qdbus org.kde.Solid.PowerManagement /org/kde/Solid/PowerManagement/Actions/BrightnessControl org.kde.Solid.PowerManagement.Actions.BrightnessControl.brightness) - 20)
Brightness Max:
qdbus org.kde.Solid.PowerManagement /org/kde/Solid/PowerManagement/Actions/BrightnessControl org.kde.Solid.PowerManagement.Actions.BrightnessControl.brightnessMax
Brightness Off:
qdbus org.kde.Solid.PowerManagement /org/kde/Solid/PowerManagement/Actions/BrightnessControl org.kde.Solid.PowerManagement.Actions.BrightnessControl.setBrightness 0
```

> **Tip:** KDE Connect Android client sorts these commands alphabeticaly, so you
> can keep then grouped by starting them with the same text


In case you are wondering from where did I get these beautifully long commands,
you can find them yourself using *qdbus* or if you prefer GUI tools then there
is *qdbusviewer*.
To be fair, it's easier to use the GUI tool if you don't know what you are
looking for because these commands sometimes well hidden or duplicated.

In short, these are [DBus](https://www.freedesktop.org/wiki/Software/dbus/)
commands, *DBus* is a software serial bus used to send messages from one
application to another.
Obviously, some of these commands are specific to KDE, so you would need to dig
in to find their counterparts for Gnome or any other DE, but if you do, please
do leave them in comments for others to use.
