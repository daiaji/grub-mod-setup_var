# A modified grub allowing tweaking hidden BIOS settings.
based on grub with [setup_var patch (invalid link now)](http://luna.vmars.tuwien.ac.at/~froemel/insydeh2o_efi/grub2-add-setup_var-cmd.patch) and [setup_var2 patch](https://habr.com/post/190354/) with setup\_var3 patch as a wordaround to duplicate Setup vairable.

As said in a [guide](https://github.com/acidanthera/AppleSupportPkg#verifymsre2) about changing hidden "CFG Lock" BIOS setting, by using a modified GRUB shell, we can change any hidden UEFI BIOS settings. But here comes some errors on my Dell XPS 8930, so I have the shell patched and added a new command `setup_var3` for this situation.

## The problem
On my PC, when I try to read/write values using the method above. It obtained a Setup variable but producing an error saying the offset is out of range. With extra investigation, I found there are two Setup variables in BIOS and the error comes from the very small (only 9 bytes in my situation) Setup variable.

With `setup_var` (without parameters), it said:

```
var name: Setup, var size: 12, var guid: 80e1202e-2697-4264 - ...

...

var name: Setup, var size: 12, var guid: ec87d643-eba4-4bb5 - ...
```

So there are two Setup variables, and if using `setup_var 0xC2` (the offset could be any value larger than first Setup variable size):

```
var name: Setup, var size: 12, var guid: 80e1202e-2697-4264 - ...

...

successfully obtained "Setup" variable from VSS (got 9 (0x9) bytes).

error: offset is out of range

```

Here comes the error, but if using a smaller offset (smaller than first variable's size):

```
var name: Setup, var size: 12, var guid: 80e1202e-2697-4264 - ...

...

successfully obtained "Setup" variable from VSS (got 9 (0x9) bytes).
offset 0x01 is: 0x15

var name: Setup, var size: 12, var guid: ec87d643-eba4-4bb5 - ...

...

successfully obtained "Setup" variable from VSS (got 4304 (0x10d0) bytes).

offset 0x01 is: 0x00
```

So without the error, we can get correct value from the "real", second "Setup" variable. This is what the patch comes for.

## The patch
This patch on two patches above adds the third `setup_var` command: `setup_var3`. It supresses the error when the Setup variable is too small and doesn't look like a real Setup variable to avoid error terminating the program resulting in cannot access real Setup variable with large offset.

USE WITH CAUTION AND ENSURE YOU HAVE EXAMINED YOU ARE ACCESSING RIGHT SETUP VARIABLE OR YOU WILL RISK BRICKING YOUR COMPUTER!!!

## Build Notes
Because I ported grub-mod-setup_var to the more modern grub2, there is nothing particularly noteworthy. Maybe you should build it on ArchLinux or Manjaro.

Build:

Use [makepkg](https://wiki.archlinux.org/index.php/Makepkg) to compile on Archlinux.

Generating modified GRUB shell:

```
grub-mkstandalone -O x86_64-efi -o modGRUBShell.efi
```
Or follow [this tutorial](https://wiki.archlinux.org/index.php/GRUB) to install grub2 on the hard disk or removable storage media.
