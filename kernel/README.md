# Kernel configs

One config per kernel version, named `<version>.config`. The `kernel_version`
dropdown in the build workflow lists the versions that have one, so adding a
version means adding a config here and an option there.

A config is produced by copying the previous version's config, running
`make ARCH=arm olddefconfig` against the new source, and then building it with
the Steam Link SDK toolchain (GCC 9.2.0) to confirm it still compiles.

`6.12.108` is the default and is the version this image has been booted and
tested on. `6.1.115` predates it and is kept only as a fallback.

## Deliberate deviations from the defaults

- `CONFIG_GCC_PLUGINS` is off. The SDK toolchain cannot build the plugins.
- `CONFIG_SECCOMP` is on in `6.12.108`. Debian userspace expects it: dhcpcd
  uses it to sandbox itself and warns when it is missing, and systemd needs it
  for the `SystemCallFilter=` hardening that ships in many unit files. The
  older `6.1.115` config predates this and still has it off.
- `CONFIG_SMP` is off. The Berlin BG2CD has a single Cortex-A9 core, so there
  is no second core to bring up.
- `CONFIG_POWER_RESET` is off. The board's device tree declares a
  `gpio-restart` node, but toggling that pin does not actually reset the SoC.
  Enabling the driver only changes reboot from "fails" to "hangs the board
  until it is power cycled", so it is left out.
