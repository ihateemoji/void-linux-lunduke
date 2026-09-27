# linux-lunduke XBPS Package

This repository contains an [xbps-src](https://github.com/void-linux/void-packages) template for packaging [Lunduke's Linux Kernel](https://github.com/BryanLunduke/lunduke-linux-kernel) on [Void Linux](https://voidlinux.org).

Lunduke's kernel is a rust-free build of upstream Linux with a desktop/VM-oriented config. The template follows Void's official kernel packaging style.

### What the template does

1. Fetches `linux-7.2.tar.xz` + `patch-7.2.6.xz` from [kernel.org](https://www.kernel.org) (same layout as Void's own kernel packages).
2. Fetches Lunduke's published config from his [GitHub repo](https://github.com/BryanLunduke/lunduke-linux-kernel) at build time (not vendored).
3. Forces `CONFIG_LOCALVERSION="-lunduke"` and keeps Rust disabled.
4. Enables `CONFIG_MODULE_SIG` + `CONFIG_MODULE_SIG_SHA512` so Void's normal `sign-file` / `mv-debug` install path works unchanged.
5. Enables `CONFIG_USER_NS` so Chromium/Firefox can use the namespace sandbox (Lunduke's config leaves this off, which causes `No usable sandbox!`).
6. Installs kernel, modules, headers, and debug symbols the same way official Void kernels do.

### Prerequisites

A working [void-packages](https://github.com/void-linux/void-packages) tree:

```sh
git clone https://github.com/void-linux/void-packages.git
cd void-packages
./xbps-src binary-bootstrap
```

See the [void-packages README](https://github.com/void-linux/void-packages#readme) and [Manual](https://github.com/void-linux/void-packages/blob/master/Manual.md) for details.

### Installation

1. Clone this repository:

   ```sh
   git clone https://github.com/ihateemoji/void-linux-lunduke
   ```

2. Copy into `srcpkgs` and create the subpackage symlinks (required by xbps-src, same pattern as `linux7.2-headers` in void-packages):

   ```sh
   cd void-linux-lunduke
   cp -r linux-lunduke /path/to/void-packages/srcpkgs/
   ```

3. Build:

   ```sh
   cd /path/to/void-packages
   ./xbps-src pkg linux-lunduke
   ```

   Kernel builds are large and slow; expect significant CPU, RAM, and disk use.

4. Install:

   ```sh
   xi -f linux-lunduke linux-lunduke-headers
   ```

   or:

   ```sh
   sudo xbps-install --repository=hostdir/binpkgs linux-lunduke linux-lunduke-headers
   ```

5. Reboot and select **7.2.6-lunduke** in your bootloader (or make it the default).

### Updating

When Lunduke publishes a new config or kernel version:

1. Bump `version` / `revision` in `template`.
2. Update the three `checksum=` lines (base tarball, patch, config). Config URL is derived from `${version}`.
3. Rebuild and reinstall.

### Layout

```
linux-lunduke/
  template          # xbps-src recipe
  files/
    mv-debug        # strip + re-sign + zstd helper (from void-packages)
linux-lunduke-headers -> linux-lunduke
linux-lunduke-dbg     -> linux-lunduke
```
