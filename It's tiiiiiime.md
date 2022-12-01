ok so this is a somewhat-beginner-friendly guide to making a linux rice (yaaay)

# Terminology
First, I want to list a bit of terminology to make things more digestible:
- Xorg, aka X, is a display server. Basically, it's the framework for any sort of window system on Linux. Due to how prevalent it is in the Linux space, it's a common prerequisite for anything involving a GUI (Graphical User Interface).
- A window manager is an application that controls the placement and appearance of windows in an X server. As with most applications on Linux, these are almost always highly customizable and give you a lot of power to make your own system.
- i3 is a *tiling* window manager, meaning that it automatically organizes windows to take up as much space as possible without overlapping each other. It has a lot of customizability without being too daunting for people new to ricing, which is why we'll be using it for this guide.
- Terminal emulators are just that -- **emulators** of a **terminal**. They let you perform command line operations even when you don't have direct access to it. They are typically built to be customized, and are thus pretty ugly when you first install and run them.

# Materials
- An installation of your favorite Linux installation (if you're setting a new one up from scratch for this tutorial, I'd recommend one without any GUI tools already installed. I'd personally suggest a minimal install of Debian, as it's fairly simple to install in a VM and generally beginner-friendly.)
- An internet connection (we'll be downloading some dependencies)
- A basic understanding of Linux (I'm assuming you know and understand how to move through directories, make and edit files, and use whatever package manager your distro uses.)

# Disclaimer
For those doing this with a computer running Linux (as opposed to a virtual machine), a word of warning: as with nearly any sort of Linux tutorial, make sure to follow directions closely. While it's not at all a likely occurrence, given the nature of what we're doing here, I'd recommend using a virtual machine if you at all feel worried about breaking your Linux installation.

# Actual guide

## Installation
Surprisingly, X doesn't have to be that complicated of an installation. First, we'll install the core server, which is basically the heart of X: (understand that all the commands here are made for Debian, so if you're using a different distro, I'm afraid I can't tell you how to download any of this.)

```
sudo apt install xserver-xorg-core
```

Once that's downloaded, we have to handle the tricky part: drivers. Sometimes, especially with VMs, it can be difficult to figure out which drivers you need. If you're like me and don't care *that* much about storage space, then you can install all the drivers you'd need with

```
sudo apt install xserver-xorg-video-all
```

If you do manage to track down the drivers you'd need, you can use

```
sudo apt install xserver-xorg-video-XXX
```

where "`XXX`" is the kind of drivers your hardware requires.

Similarly, we need hardware-specific input device drivers, and similarly, we can just install all of them with

```
sudo apt install xserver-xorg-input-all
```

Although reportedly `evdev` will handle most hardware.

Now, we're out of driver territory, so you can install these packages without any second thoughts:

- `x11-server-utils`, which gives you utilities for handling things like your keyboard layout and window sizes
- `x11-xkb-utils`, which gives you another utility for setting keyboard layouts that's useful in different situations
- `x11-utils`, which gives you a bunch of useful utilities for handling and debugging basic X things (and for customizing i3)
- and `xinit`, which is a helper script for starting an X server.

Finally, we want a terminal emulator and a window manager. We already know what our window manager is, so we can `sudo apt install i3`, but the terminal emulator is mostly up to personal choice. If you want a terminal emulator that's just easy to setup and use, install `xterm`. If you want one that's much more customizable, but a bit more obtuse, install `rxvt` or `rxvt-unicode`.

Now we have everything we need. Just make a `.xinitrc` and include the following:

```
#!/bin/sh

exec i3
```

Besides the shebang at the top (yes, that is [the real, actual name of that](https://en.wikipedia.org/wiki/Shebang_(Unix))) this just tells xinit/startx to run our window manager when starting up the X server.

## First run
Run `startx` and if everything is set up correctly, you should see a blank screen with a bar of text at the bottom. Give yourself a pat on the back for getting this far.

There should be one window asking about whether you'd like to generate a config file or not. The alternative is being forced to use the defaults, and we're trying to change those, so press Enter to generate the config file. It'll then ask you about what default modifier you'd like to use -- this'll be the button that you use for most i3-related keyboard shortcuts (of which there are a lot). Pick whichever one seems more convenient at the moment, you can always change it later. Once that's selected, the window will disappear and you should have an empty screen with some text at the bottom.

This is what the default i3 configuration looks like. It's not glamorous, but the default configuration is focused on function over form. We can fix that, though. Hold down your default modifier key (which I'm just going to refer to as Mod from now on) and press Enter to open a terminal. It'll likely look bad, but that's yet another thing we can fix.

I can't go over all of i3's functionality -- there's a lot of it and you get to decide what it is since you can change almost everything about how it works and add new stuff yourself. However, you need to know some stuff if you plan on using it a lot, so I'll run down the basic things you'll want to know. Note that all of these are the default, so if you change your config files, these can change.

- Again, Mod+Enter opens a terminal window. There is a hierarchy of terminal emulators built into i3, so whichever installed emulator is the highest on this (largely arbitrary) list gets opened. You can change this manually by editing the Mod+Enter keybind in the config to just run your emulator of choice.
- Mod+D gives you a list of applications to run. By default, this is a huge list of every command that can be run on your installation, which isn't very helpful. Many of these won't open any windows, and there's no practical reason to run them without any parameters. There's a way to change this that'll be explained later.
- Mod+[1-0] changes the workspace you're using. Workspaces are seperate screens with different states, meaning you can store different windows in each of them. The number in the bottom left (assuming you haven't done anything yet) shows you that you're in workspace 1, and more numbers will be added for each workspace with windows in them. You can move a window to a workspace using Mod+Shift+[1-0], where [1-0] is the number of the workspace you want to move it to (0 = 10).
- Mod+Shift+Space will turn a tiling window into a floating window. These will always display above any tiling windows and can be reverted to a tiling window with the same shortcut.
- Mod+[Arrow Keys] will move focus from one window to another, prioritizing the window in the direction of the key you pressed if possible. Mod+Shift+[Arrow Keys] will move the window that way, shifting any other windows to make room for it.
- Mod+Shift+R reloads i3, allowing you to test changes made to your i3 config file. Don't worry, your layout and session will be preserved.
- Mod+Shift+E lets you exit the i3 session (after a warning). If your X session is set up correctly this should let you get back to the standard command prompt.

If you want to know more about the default layout, how i3 works, or how to customize things, I'd highly recommend reading the [i3 User's Guide](https://i3wm.org/docs/userguide.html).

## Ricing
Okay, now we can finally get into the customization side of things. Now, obviously some people will want certain things out of their desktop and others are content with things the way they are. I don't want to force you into doing anything, so I'll give you a list of different things that you can dive into or skip over if you're really uninterested. I suggest at least reading all of them so you know how to do something if you want to do it later, but there's no requirement to.

### Disabling Tiling
Personally, I'm a big fan of the tiling system i3 uses. It removes all the hassle of moving and resizing windows, makes multitasking simpler, and easily allows users to navigate soley with their keyboard. But a lot of people just aren't a fan, and that's okay! Thankfully, i3 has a way to instead force all windows to start in floating mode, if you really want to.

Add `for_window [class="[.]*"] floating enable`, and reload the config. Now any new windows created will start floating. You can also delete the line for the `bindsym` for `$mod+Shift+Space`, which will disable the keybind for switching windows between tiling and floating mode.

### Fonts
Most of the default fonts here aren't awful per se, but they aren't massively appealing by any means. Let's get some new ones.

Either locate the link and download whichever font file through some cli magic or download Firefox to make this a more bearable process. Then look for a font you like. I, personally, am partial to [cozette](https://github.com/slavfox/Cozette), but if you're not into pixel fonts, then try a [Nerd Font](https://www.nerdfonts.com). They're a bunch of tried-and-true fonts that have lots of extra symbols patched in so you can add nice little icons to your cli tools or status bar.

Then, once you have your font files (of whatever type) simply copy or move them all to `/usr/local/share/fonts`, which will install them for all users on the system. You'll probably have to relog for the fonts to work, but if you're too good for that you can install `fontconfig` (which I think might actually be included w/ xorg but better safe than sorry) and then run `fc-cache -rv`.

If you want these fonts to appear in i3, find the line that already exists starting with `font`. You can change the first parameter to whatever font you want, just make sure to add the `pango:` prefix for any non-bitmap fonts. If you're using `pango:`, you can also specify a font size in pixels, which is the second parameter.

e.g. `font pango:CozetteVector 12`

### Changing Colors
I won't sugarcoat it: i3's color scheme is pretty ugly. How about we fix it?

i3's system for setting different colors is extensive and simple, but it's a lot of commands repeating the same colors over and over again. This is why I (and most i3 tutorials) recommend setting some variables to keep all your colors in order. The number can vary, but here's the setup I have:

```
set $bg-color            #2f343f
set $inactive-bg-color   #2f343f
set $text-color          #f3f4f5
set $inactive-text-color #676E7D
set $urgent-bg-color     #E53935
```

This might seem kind of silly, but trust me, when you look at the actual color setting commands, you'll be glad you don't have to update each of them manually when trying out new colors:

```
# window.colors
#                       border             background         text                 indicator
client.focused          $bg-color          $bg-color          $text-color          #00ff00
client.unfocused        $inactive-bg-color $inactive-bg-color $inactive-text-color #00ff00
client.focused_inactive $inactive-bg-color $inactive-bg-color $inactive-text-color #00ff00
client.urgent           $urgent-bg-color   $urgent-bg-color   $text-color          #00ff00
```
*Note that the two lines at the top are just to help remember the order of parameters and what these are affecting.*

Those color definitions are in hex RGB, so you'll probably want a color picker to help with getting the values to put down. You can change all of the colors in similar ways, but I just put down the window colors. If you want to know how to set colors for things like the bar, I once again recommend checking the [user guide](https://i3wm.org/docs/userguide.html). (It's very helpful.)

### Wallpapers
One of the staples of even mainstream desktops, and for good reason. There's a couple of ways to go about this, but we'll talk about the general default for pulling this off.

`feh` is an image viewer that can also be used for wallpapers. Install it through your package manager of choice and add a line to:

```
exec_always feh --bg-scale [path to the image file]
```

`exec_always` ensures that it will be run even when reloading the i3 config, whereas usually `exec` calls will only be run on the initial startup of the session. This'll make it easier to try new backgrounds without having to relog. The `--bg-scale` option can be replaced with several others (`--bg-tile`, `--bg-center`, `--bg-max`, `--bg-fill`) that each treat the image differently.

### Keyboard Shortcuts
i3's default control scheme can take some getting used to, and there are a lot of things you might wish worked differently or maybe don't want at all. Let's look into changing the keyboard shortcuts and making new ones.

`bindsym` and `bindcode` both let you set a keybind. Their first parameter is what the keybind actually is, with pluses in-between all required keys. Some have special names, so you may have to do some digging to bind certain keys. The difference between the two is that `bindsym` uses names, which are usually the literal character but sometimes differs, and `bindcode` uses keycodes, which are numbers assigned to each key on the keyboard. You'll be using `bindsym` most of the time, but `bindcode` can have its uses.

### Status Bar
i3 has a submodule called i3-status, responsible for making that cool little bar at the bottom work. Unfortunately, the default configuration is complicated and a little bloated depending on your hardware.

You can copy the template used from `/etc/i3status.conf` to `~/.config/i3status/` under the name `config`. Most naming is self-explanatory, so you can start with deleting the stuff you don't need, but then you might want to visit [the man page](https://i3wm.org/docs/i3status.html) for more info on how to customize things further

### Screenshots
After you're done with all of this stuff, you're probably going to want to show off what you've made. If you want a quick-to-reach screenshot button, use this.

There's a package called `scrot` that makes taking X screenshots crazy simple. Install it, and try adding a line to bind something to `exec scrot`. You can customize this further by changing what the name will be, where it's stored, etc. Here's the line I used to make my screenshot button, bound to Mod+\\ (backslash), which puts a screenshot in the `~/screenshots` folder after asking for a name:

```
bindsym $mod+backslash exec i3-input -F "exec scrot "/screenshots/%s.png" -P "Screenshot name: "
```

## Next Steps
Well, that's all I really have for you. I hope the adventure was worth it, and you learned something interesting, or at least made a cool desktop. If you're craving more, here's some stuff to check out for inspiration or even more possibilities.

### Look into Customizing Your Terminal Emulator
A rice is never fully complete without a nice terminal emulator to go with the theme! Unfortunately, this would be best researched on your own, since the customization process can vary depending on which emulator you're using. You end up using the terminal a lot even with a desktop; it's worth making it look pretty!

### Look into AwesomeWM
AwesomeWM is, as the name suggests, a really awesome window manager that gives you a different feature set and, to some extents, even more customizability. Its configuration system is a lot different than i3's (it uses an actual programming language called Lua to create everything on-screen) and is much less beginner-friendly -- especially if you don't know Lua already -- but the flexibility and capabilities it gets from this system are amazing. Of course, in the end, the best one is the one that *you* like the most.

### Check out Other People's Rices
If you're looking for some inspiration, why not check out the [unixporn subreddit](https://www.reddit.com/r/unixporn/)? Yes, I'm fully aware of what it sounds like, but I promise you, it's actually a really nice community filled with passionate people who love making awesome desktops. You can find a lot of cool packages, window managers, ideas, and techniques through here. A lot of people even share their configuration files so you can copy their look or see how they do something.