**Unofficial** Linux build of Sable @VERSION@ that fixes H.264/AAC video playback.
Not affiliated with or endorsed by the Sable project. Please don't report problems
with this build upstream; open them on this fork instead.

## What this fixes

[SableClient/Sable#1535](https://github.com/SableClient/Sable/issues/1535): on Linux,
videos show *"Failed to load video!"* even though the same files play in Element,
Fractal, or Sable on Windows and macOS.

**Cause.** The official Linux builds embed CEF (Chromium). Its `libcef.so` is compiled
with only royalty-free codecs (VP8, VP9, AV1, Opus) and has no H.264 or AAC decoder.
Most video people share is H.264 with AAC audio, so Chromium rejects it with
`DEMUXER_ERROR_NO_SUPPORTED_STREAMS`. Windows and macOS don't have the problem because
Sable uses the operating system's webview there, which uses system codecs.

**Fix.** This build uses Sable's other built-in runtime, **WebKitGTK**, instead of CEF.
WebKitGTK plays media through **GStreamer**, so it uses the codecs installed on your
own system, the same way Fractal does. The application source code is unchanged;
only the build target differs. No proprietary codecs are included in these downloads.

## Supported systems

x86_64 and aarch64 (ARM64), on distros with **glibc 2.35 or newer**: Ubuntu 22.04+,
Linux Mint 21+, Debian 12+, Fedora 36+, and current Arch.

Pick the file for your architecture. The commands below use `x86_64`; on ARM, use the
`aarch64` file instead.

## Install

**Debian, Ubuntu, Mint (`.deb`)**

    sudo apt install ./Sable-@VERSION@-wry-linux-x86_64.deb

This also installs WebKitGTK and the GStreamer codec plugins (`gstreamer1.0-libav`),
so video works straight away.

**Fedora (`.rpm`)**

Fedora's own `ffmpeg-free` has no H.264 decoder, so first enable RPM Fusion and its
codecs by following [RPM Fusion's multimedia guide](https://rpmfusion.org/Howto/Multimedia). Then:

    sudo dnf install ./Sable-@VERSION@-wry-linux-x86_64.rpm

Without the RPM Fusion codecs the package won't install, or H.264 video still won't play.

**Arch and other distros (`.AppImage`)**

Install the GStreamer codec plugins from your distro first. On Arch:

    sudo pacman -S gst-libav gst-plugins-good gst-plugins-bad

Then run the AppImage directly:

    chmod +x Sable-@VERSION@-wry-linux-x86_64.AppImage
    ./Sable-@VERSION@-wry-linux-x86_64.AppImage

The AppImage uses your system's GStreamer plugins, so video only works if `gst-libav`
(or your distro's equivalent) is installed.

**Tarball (`.tar.gz`)**

Same layout as the official tarball, minus CEF's `runtime/` folder: the `sable` binary
plus a `share/` folder with a desktop entry and icons. It needs WebKitGTK 4.1 and the
GStreamer libav plugin from your distro, as above.

## Updates

This build updates itself from **this fork's** releases only, and only accepts updates
signed with this fork's key. It will never switch you back to an official CEF build.
For `.deb` and `.rpm` installs, updating asks for your password.

## Known limitations

- **Different runtime from the official builds.** Behavior that depends on CEF may differ.
- **Encryption.** Logging in creates a new device. To read older encrypted messages,
  restore your key backup in *Settings → Devices → Encryption Backup*.

## Source

Built from commit `@COMMIT@` on branch
[`fix/linux-h264-video`](https://github.com/WACOMalt/Sable/tree/fix/linux-h264-video),
based on upstream `v@VERSION@`, by this repository's `wry-release.yml` workflow.
Licensed AGPL-3.0.
