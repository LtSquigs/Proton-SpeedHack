Proton Speed Hack
------------

**Proton Speed Hack** is a Steam Play compatability tool based off of Proton that adds speedhack capabilities to any game. This speedhack is based off of Cheat Engine's speedhack code.

This tool binds a keyboard button with which the user can press to speed up how fast a game is running.

This functionality can be modified by various configuration methods. You can change the speed factor of the hack, what buttons are used to activate the hack, and wether or not the button must be held to activate or if it can be toggled on/off.

If you want to build this from source, you can find the original README for proton [here](https://github.com/ValveSoftware/Proton).

Please do not open any issues against the Proton repo if you encounter issues with this tool. Speedhacks are not guaranteed to be stable in all games. Additionally, do not use this in any scenario where you would not use Cheat Engine.

- [Installation](#installation)
  - [Installing Compatability Tool](#installing-compatability-tool)
  - [Enabling Game + Gamepad](#enabling-game--gamepad)
- [Configuration](#configuration)
  - [Global Configuration](#global-configuration)
  - [Game Configuration](#game-configuration)
- [Key Values](#key-values)
- [Developers: Signal Controls](#developers-signal-controls)

Installation
------------

### Installing Compatability Tool

In order to install the compatability tool you should do the following (if on a standard Steam Deck setup)

1. Download a release from the [Releases](https://github.com/LtSquigs/Proton-SpeedHack/releases) page.
2. Create a `~/.steam/root/compatibilitytools.d` directory if it does not exist.
3. Extract the release tarball into `~/.steam/root/compatibilitytools.d/`.
   * `tar -xf proton-speedhack-<version>.tar.gz -C ~/.steam/root/compatibilitytools.d/`
4. Restart Steam.

This should create a folder on your hard drive that looks something like `~/.steam/root/compatibilitytools.d/proton-speedhack-<version>/`

Inside this folder there will be a file called `speedhack.json`. You can edit this file to change the global configuration options for the speedhack as [described below](#global-configuration).

### Enabling Game + Gamepad

One the compatability tool has been installed, you can enable the speedhack on a game by doing the following:

1. Access the Steam Games Properties.
2. At the bottom of the `Compatibility` tab, Check `Force the use of a specific Steam Play compatibility tool`, then select the version of Proton Speedhack you have installed.
3. Update your Gamepad Bindings for the game to bind a button on your gamepad to the Activation Key for the speedhack (`Page Up` by default)
4. Launch Game


Configuration
------------

There are two main ways to change the configuration of this speedhack tool: Config Files (global), Environment Variables (Game-Specific).

### Global Configuration

The following files are checked for the global configuration of the speedhack:

`~/.speedhack/speedhack.json`

`<install_dir>/speedhack.json`

The default configuration file should look something like this:

```
{
    "speed": 2.5,
    "toggle": 0,
    "keys": "page_up"
}
```

`speed` is a non zero value that indicates by how much the speedhack speeds up the game.

`toggle` is either `0` or `1` to indicate if the activation button should either be held to keep the hack active (`0`) or if the button toggles the hack on and off (`1`)

`keys` is a comma seperated list of 1-4 key names as defined below in [Key Values](#key-values).

So if you wanted to change the speed to 4x, turn on toggle mode, and change the activation key to Shift + Page Down the config file would look like:

```
{
    "speed": 4.0,
    "toggle": 1,
    "keys": "shift,page_down"
}
```

### Game Configuration

For game specific configurations you can use the following Environment variables to configure the options for the speedhack:

```
SPEEDHACK_SPEED=2.5
SPEEDHACK_TOGGLE=0
SPEEDHACK_KEYS=page_up
```

The fields for these variables accept the same values as described in the global configurations above.

You can set these for a game by changing the Launch Options for the game.

So if you wanted to change the speed to 4x, turn on toggle mode, and change the activation key to Shift + Page Down you wolud edit the Launch Options for the game to look like this

```
SPEEDHACK_SPEED=4 SPEEDHACK_TOGGLE=1 SPEEDHACK_KEYS=shift,page_down %command% 
```

Alternatively you can create a game specific configuration json file in the following path:

`~/.speedhack/<game_id>.json`

Key Values
------------

Key String | Keyboard Key 
:-----:|:------:
`0` - `9` | 0 - 9
`a` - `z` | A - Z
`f1` - `f12` | F1-F12
`semicolon` | Semicolon (;)
`plus` | Plus (+)
`comma` | Comma (,)
`minus` | Minus (-)
`period` | Period (.)
`forward_slash` | Forward Slash (/)
`back_slash` | Back Slash (\\)
`left_bracket` | Left Bracket ([)
`right_bracket` | Right Bracket (])
`quote` | Quote (")
`tilde` | Tilde (~)
`back` | Backspace
`tab` | Tab
`enter` | Enter/Return
`shift` | Shift
`control` | Control
`alt` | Alt
`left_win` | Left Windows
`right_win` | Right Windows
`caps_lock` | Caps Lock
`esc` | Escape
`space` | Space
`page_up` | Page Up
`page_down` | Page Down
`end` | End
`home` | Home
`insert` | Insert Button
`delete` | Delete Button
`up` | Up Arrow
`down` | Down Arrow
`left` | Left Arrow
`right` | Right Arrow
`num0` - `num9` | Numpad 0 - 9
`multiply` | Numpad Multiply
`add` | Numpad Add
`separator` | Numpad Seperator
`subtract` | Numpad Subtract
`decimal` | Numpad Decimal
`divide` | Numpad Divide
`numlock` | Numlock

Developers: Signal Controls
------------

When running the Speedhack version of Proton, we listen to two signals that can be sent to the running `steam.exe` process to update the configuration of the speedhack.

The intention of these signals is to allow anyone to develop extensions/plugins (such as with Steam Decky Loader) that can update/manage the configurations of the Speedhack tool.

The two signals we listen for are `SIGUSR1` and `SIGUSR2` and both are expected to send an integer payload with them (using `sigqueue`) which has a specific format to update the configuration.

The `SIGUSR1` signal is used to update the `speed` and `toggle` settings of the speedhack.

The integer payload for `SIGUSR1` is expected to have the following structure:

Bit | Value
--:|:--
1 | Toggle Update Flag. Set if the `toggle` value should be updated
2 | Value Of `toggle` if update flag set
3 | Toggle hack flag. If 1 toggles the hack on and off.
4 | Speed Update Flag. Set if the `speed` value should be updated
5-32 | Binary value of `speed` x 10. e.g. if new speed is `1.5` this should be `15`. Should be an `unsigned integer`


The `SIGUSR2` signal is used to update the `keys` settings of the speedhack.

The integer payload for `SIGUSR2` is expected to have the following structure:

Bit  | Value
--:  | :--
1-8  | Keycode of first activation button.
9-16 | Keycode of second activation button.
17-24| Keycode of third activation button.
25-32| Keycode of fourth activation button.

The keycodes for these buttons should be [Windows Virtual Keycodes](https://learn.microsoft.com/en-us/windows/win32/inputdev/virtual-key-codes).

If a keycode should not be set (e.g. you have < 4 buttons to register), then the value of the keycode should be `0`

<!-- Target:  GitHub Flavor Markdown.  To test locally:  pandoc -f markdown_github -t html README.md  -->
