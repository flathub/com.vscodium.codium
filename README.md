# Flatpak VSCodium

## Issues
Please open issues under: https://github.com/flathub/com.vscodium.codium/issues

## FAQ

### Wayland vs X11
If you have problems to start this flatpak under X11 please run one of these two
commands and try again

```bash
# Only disable wayland to force fallback on xwayland
flatpak override --user --nosocket=wayland com.vscodium.codium
# or
# Disable wayland and enable X11
flatpak override --user --socket=x11 --nosocket=wayland com.vscodium.codium
```

Is also recommended to run `flatpak permission-reset com.vscodium.codium`

### About the access to the host filesystem
Note that vscodium is granted *full access to your home directory*.
You can use `flatpak override` to locally adjust this if you prefer to sandbox vscodium file system access:
```
flatpak override --user com.vscodium.codium --nofilesystem=host
# now manually grant accesss to the folder(s) you want to work in
flatpak override --user com.vscodium.codium --filesystem=~/src
```

This version is running inside a _container_ and is therefore __not able__
to access SDKs on your host system!

### To execute commands on the host system, run inside the sandbox:

  `$ flatpak-spawn --host <COMMAND>`

  or

  `$ host-spawn <COMMAND>`

  - Most users seem to report a better experience with `host-spawn`

Note that this runs the COMMAND without any further host-side confirmation.
If you want to prevent such full host access from inside the sandbox, you can use `flatpak override` as follows:
```
flatpak override --user com.vscodium.codium --no-talk-name=org.freedesktop.Flatpak
```
### Where is my X extension? AKA modify product.json

Since are serveral ways to achieve this the better is to use [vsix-manager](https://open-vsx.org/extension/zokugun/vsix-manager)

### Host Shell

To make the Integrated Terminal automatically use the host system's shell,
you can add one of the following configurations for flatpak-spawn or host-spawn to the settings of vscodium:


`flatpak-spawn`

```json
  {
    "terminal.integrated.defaultProfile.linux": "bash",
    "terminal.integrated.profiles.linux": {
      "bash": {
        "path": "/usr/bin/flatpak-spawn",
        "args": ["--host", "--env=TERM=xterm-256color", "bash"],
        "icon": "terminal-bash",
        "overrideName": true
      }
    },
  }
```

`host-spawn`

```json
  {
    "terminal.integrated.defaultProfile.linux": "bash",
    "terminal.integrated.profiles.linux": {
      "bash": {
        "path": "/app/bin/host-spawn",
        "args": ["bash"],
        "icon": "terminal-bash",
        "overrideName": true
      }
    },
  }
```

- You can change **bash** to any terminal you are using: zsh, fish, sh.
- `overrideName` allows for the 'name' (or whatever you set it to) of the shell you're using to appear (e.g. normally zsh, fish, sh).
### SDKs

This flatpak provides a standard development environment (gcc, python, etc).
To see what's available:

```bash
  $ flatpak run --command=sh com.vscodium.codium
  $ ls /usr/bin (shared runtime)
  $ ls /app/bin (bundled with this flatpak)
```
To get support for additional languages, you have to install SDK extensions, e.g.

```bash
  $ flatpak install flathub org.freedesktop.Sdk.Extension.dotnet
  $ flatpak install flathub org.freedesktop.Sdk.Extension.golang
  $ FLATPAK_ENABLE_SDK_EXT=dotnet,golang flatpak run com.vscodium.codium
```
You can use

```bash
  $ flatpak search <TEXT>
```
to find others.

### Run flatpak codium from host terminal

If you want to run `codium /path/to/file` from the host terminal just add this
to your shell's rc file

```bash
$ alias codium="flatpak run com.vscodium.codium "
```

then reload sources, now you could try:

```bash
$ codium /path/to/
# or
$ FLATPAK_ENABLE_SDK_EXT=dotnet,golang codium /path/to/
```

### Git LFS support

To use Git LFS with VSCodium's integrated Git support, you will need the `com.visualstudio.code.tool.git-lfs` addon.

```bash
  $ flatpak install flathub com.visualstudio.code.tool.git-lfs
```

You might need to reinstall the Git LFS hooks in each repo with `git lfs install`.

## Deprecation of arm (32 bits) builds

> - `armhf/armv7` builds have their particular branch named __armv7__ and will
be deprecated on 2021 due to the `org.freedesktop.Sdk` versions >=
[20.08](https://gitlab.com/freedesktop-sdk/freedesktop-sdk/-/tags/freedesktop-sdk-20.08.0)
disable armv7 builds. You can follow the discussion
[here](https://gitlab.com/freedesktop-sdk/freedesktop-sdk/-/issues/1105).
> - This particular branch is based on `org.freedesktop.Sdk` == 19.08 that be
supported until 2021, you can read more about it
[here](https://wiki.gnome.org/GUADEC/2019/Hackingdays/FreedesktopSdk/Notes).

## Related Documentation

- https://github.com/VSCodium/vscodium/blob/master/docs/index.md
- https://code.visualstudio.com/docs#vscode
