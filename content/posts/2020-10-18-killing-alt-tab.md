---
title: Killing Alt + Tab
date: "2020-05-18T07:00:00Z"
categories: [ Tools ]
tags: [ productivity ]
---
Personal computing became viral when [Xerox](https://en.wikipedia.org/wiki/PARC_(company)) invented GUI(Graphical User Interface) (Yes not Apple or Microsoft) with their [Xerox Alto](https://en.wikipedia.org/wiki/Xerox_Alto), which is also the first modern computer to utilise a [mouse](https://en.wikipedia.org/wiki/Computer_mouse) and created the metaphor [Desktop](https://en.wikipedia.org/wiki/Desktop_metaphor). Productivity with personal computers is an interesting area which we all wished we had. 

## The Problem

The ability to run multiple programs simultaneously is backed by [Window Manger](https://en.wikipedia.org/wiki/Window_manager#:~:text=A%20window%20manager%20is%20system,help%20provide%20a%20desktop%20environment.) (one of the system software). Most Operating Systems provides their own implementation of *Window Manager*, like the popular [X window system](https://en.wikipedia.org/wiki/X_Window_System) and [Wayland](https://en.wikipedia.org/wiki/Wayland_(display_server_protocol)) for UNIX based operating systems.

User Inputs of a typical computing systems are ***Keyboard*** and ***Mouse***. While both of them excels at their use cases, keyboards provide more functionalities. Performing multiple tasks simultaneously by a user requires more effort. The main problem here is *Context Switching* (switching between programs). It should be seamless, less distracting and convenient.

While most of the window managers provides various functionality, they are targeted at common users (*User-Friendly*). Which is obviously not suitable for power users. The biggest pain for power users is ***Alt + Tab***, a keybinding (keyboard shortcut) for switching between windows (programs). The *Context Switching* here is really heavy, distracting and is counter productive.

## The Solution

One of the popular solution is [Tiling window manager](https://en.wikipedia.org/wiki/Tiling_window_manager), a tool which arranges windows in you're workspace automatically. But it will take time to get used to it and needs practice.

To getting stared you can use ***Custom keybinding*** to switch between windows, which you can create with these great tools

- [AutoKey](https://github.com/autokey/autokey) for Linux based systems.
- [AutoHotKey](https://github.com/AutoHotkey/AutoHotkey) for Microsoft Windows.

> Key bindings are actions assigned to keyboard keys or key combinations. The default set of key bindings including '<Alt>-Tab' to switch between windows and 'Print Scrn' to take a screenshot.

For example with these tools you can assign `RAlt + c`  key binding to execute the following tasks

- *Switch* to Chrome window (If a chrome instance is running)
- *Launch* Chrome (if a chrome instance is not running) and switch to Chrome window
- *Cycle* through Chrome window (if multiple instance of chrome is running)

All the above 3 tasks (launch, switch, cycle) with a single key binding

Getting started to these tools is pretty simple, it took me ~10 minutes to setup keybindings for my daily usage programs (8 programs totally).

I am using Ubuntu as my daily driver, so moving forward, we will be looking more into [AutoKey](https://github.com/autokey/autokey).

## Autokey setup

Installing Autokey is pretty straight forward and have detailed [documentation](https://github.com/autokey/autokey/wiki/Installing#contents). Once the setup is complete, you can use the GUI to create the scripts. With Autokey every script is itself a python program, so if you're already familiar with python you can do wonders with Autokey, or if you're like me, new to python you can get started with writing script for switching between windows pretty easily.

Start writing scripts and attach a *Hot Key* to your script.

We will be also using autokey + [jumpapp](https://github.com/mkropat/jumpapp) for window switching, jumpapp is a simple wrapper around [wmctrl](http://tripie.sweb.cz/utils/wmctrl/).

> **Jumpapp** is a run-or-raise application switcher for any X11 desktop

> The **wmctrl** program is a UNIX/Linux command line tool to interact with an EWMH/NetWM compatible X Window Manager. It can be used, for example, to obtain information about the window manager, to get a detailed list of desktops and managed windows, to switch and resize desktops, to make windows full-screen, always-above or sticky, and to activate, close, move, resize, maximise and minimise them.

wmctrl is a powerful tool and have to have complete control over windows. One of the great use case for wmctrl is [scrpcy-switch](#scrcpy) 

## Showcase

*Note : All these showcase's outcome can be achieved also via [AutoHotKey](https://github.com/AutoHotkey/AutoHotkey)*

### Window switch

Switching between windows is a simple script with just one line command, most of the heavy work is carried over by jumpapp

```python
command = 'jumpapp -r google-chrome'
system.exec_command(command, getOutput=False)
```

The above command will

- *Switch* to Chrome window (If a chrome instance is running)
- *Launch* Chrome (if a chrome instance is not running) and switch to Chrome window
- *Cycle* through Chrome window (if multiple instance of chrome is running)

These same command can be used for any programs by simply changing the name of the program in the command.

These names are **not** usually what you see in the window title or application name. To get the program name you can run the command `wmctrl -lx` in you're terminal which lists all the currently opened windows and its name.

```shell
$ wmctrl -lx
0x05000008  0 evince.Evince		typora.pdf
0x05200001  0 typora.Typora		killing-alt-tab.md - Typora
0x05c00007  0 google-chrome.Google-chrome	wmctrl - A command line tool - Google Chrome
```

> 0x05c00007  0 **google-chrome**.Google-chrome	wmctrl - A command line tool - Google Chrome

You can notice name for Chrome is `google-chrome`, and this is what you need to use in autokey scripts. These names are know as `WM_CLASS`.

### Global google search

Being able to search any selected text by activating a python script (below) everytime I pressed a set of keyboard buttons.

```python
import webbrowser
base="http://www.google.com/search?q="
phrase=clipboard.get_selection().strip()
webbrowser.open_new_tab(base+phrase)
```

*[credits](https://github.com/autokey/autokey/wiki/Scripting#googling-query-from-anywhere)*

These can be useful in many ways like getting meaning for a word, google search on error messages, etc.

### Typora

I like when I can see the preview of the markdown I am writing, which most of the programs lacks really bad, like [Trello](https://trello.com/en). So this simple autokey script solves this issue by launching the content in [typora](https://typora.io/) for me,

```python
snip = clipboard.get_selection()

file_path_cmd = 'echo "$HOME/tmp/typora-temp.md"'
file_name = system.exec_command(file_path_cmd, getOutput=True)


file = open(file_name, 'w+')
file.write(snip)
file.close

typora_cmd = 'typora ' + file_name
system.exec_command(typora_cmd, getOutput = False)
```

The idea is to copy (to [clipboard](https://en.wikipedia.org/wiki/Clipboard_(computing))) the markdown content and execute this keybinding which writes content from the clipboard to a tmp file (because typora needs a file as an argument)  `typora-temp.md`  and launch the file in typora.

> I use Typora to write these blogs

### Jitsi

We use [jitsi meet](https://jitsi.org/jitsi-meet/) intensively for having meetings, pair programming and discussions. It is a web app, were need to launch a web browser and enter the link to join the room. We use jitsi daily and it was repetitive task to getting into the room, so we have written a autokey script to simply the process.

```python
import webbrowser

def open_jitsi(url):
    webbrowser.open_new_tab(url)
    clipboard.fill_clipboard(url)


jitsi_meet="https://meet.jit.si/"

rooms = [
    'room1',
    'room2',
    'room3',
    'others'
]
roomsCode, room = dialog.list_menu(
    options = rooms,
    title = 'Jitsi meet',
    message = 'Rooms',
    default = rooms[0],
    width = '300',
    height = '350'
)

if not roomsCode:
    if(room == rooms[-1]):
        roomInputCode, inputRoom = dialog.input_dialog(
            title='Jitsi meet',
	        message='Enter room', 
	        default= rooms[0]
        )
        if not roomInputCode:
            open_jitsi(jitsi_meet + inputRoom)
    else:
        open_jitsi(jitsi_meet + room)
    
```

Jitsi doesn't have any GUI to remember the frequently used rooms, this can be really difficult to join a discussion quickly. So this script creates the GUI I need which displays my frequently used rooms, also takes room as input from me for new rooms.

![jitsi-blog](/assets/img/posts/jitsi-blog.gif)

### scrcpy

Being an android developer, I will be testing the apps in an real device very often and [scrcpy](https://github.com/Genymobile/scrcpy)  is a tool which casts the screen you're system and lets you control the device from the system.

This script is pretty advanced where, it will launch scrcpy window for devices one by one on key press, and cycles through the windows if all the devices have a scrcpy window ready.

```python
def scrcpy(device_serial):
    return f'scrcpy --shortcut-mod=lctrl+lalt --window-x 1460 --window-y 150 --window-width 365 --window-height 730 --window-title scrcpy-{device_serial} -Sw --always-on-top -s {device_serial}'

def devices(device):
  return device.split('\t')[0]

adb_devices = "adb devices"  
adb_devices_output = system.exec_command(adb_devices).split('\n') 

adb_devices_output.pop(0)  #removing 'List of devices attached'
adb_devices_output.pop() #removing 'last emoty line

devices = list(map(devices, adb_devices_output))

wmctrl = "wmctrl -lx"
wmctrl_output = system.exec_command(wmctrl, getOutput=True)

for device in devices:
    if device not in wmctrl_output:
        system.exec_command(scrcpy(device), getOutput=False)
        break

command = 'jumpapp -r scrcpy' 
system.exec_command(command, getOutput=False)
```

>  The script will launch the scrcpy window only for devices which has not yet created a scrcpy window, but how ?

scrcpy supports setting custom window title (`--window-title`), so we set the `device_serial` as window title, then use `wmctrl -lx`  to get list of window title, then we will have currently active `device_serial` , so we can simply skip scrcpy window for devices already have a scrcpy window.

![scrcpy_switch](/assets/img/posts/scrcpy_switch.gif)



### Credits

Thanks to [Shrayas](http://shrayas.com/) for reviewing this post.
