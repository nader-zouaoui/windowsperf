# Build wperf-driver

You can build `wperf-driver` project from command line:

```
> devenv windowsperf.sln /Rebuild "Debug|ARM64" /Project wperf-driver\wperf-driver.vcxproj
```

# Kernel Driver Installation
Kernel driver can be installed and removed on ARM64 machine with DevCon command.

First change directory to directory where your `wperf-driver` files (`wperf-driver.cat`, `wperf-driver.inf`, and `wperf-driver.sys`) are:

```
> cd D:\Workspace\wperf-driver
> dir
28/10/2022  10:07             2,459 wperf-driver.cat
28/10/2022  10:06             7,738 wperf-driver.inf
28/10/2022  10:06            33,712 wperf-driver.sys
```

Use `DevCon Install` command to install driver and `DevCon Status` to check driver's status. You can also remove driver using `DevCon Remove` command. See examples below.

## DevCon Install

Creates a new, root-enumerated devnode for a non-Plug and Play device and installs its supporting software. Valid only on the local computer. See [DevCon Install](https://learn.microsoft.com/en-us/windows-hardware/drivers/devtest/devcon-install) article for more details.

```
> devcon install wperf-driver.inf Root\WPERFDRIVER
Device node created. Install is complete when drivers are installed...
Updating drivers for Root\WPERFDRIVER from D:\Workspace\wperf-driver\wperf-driver.inf.
Drivers installed successfully.
```

Other uses of `devcon` command are:

## DevCon Remove

Removes the device from the device tree and deletes the device stack for the device. As a result of these actions, child devices are removed from the device tree and the drivers that support the device are unloaded. See [DevCon Remove](https://learn.microsoft.com/en-us/windows-hardware/drivers/devtest/devcon-remove) article for more details.

```
> devcon remove wperf-driver.inf Root\WPERFDRIVER
ROOT\SYSTEM\0001                                            : Removed
1 device(s) were removed.
```

## DevCon Status

Displays the status (running, stopped, or disabled) of the driver for devices on the computer. See [DevCon Status](https://learn.microsoft.com/en-us/windows-hardware/drivers/devtest/devcon-status) article for more details.

```
> devcon status wperf-driver.inf Root\WPERFDRIVER
ROOT\SYSTEM\0001
    Name: WPERFDRIVER Driver
    Driver is running.
1 matching device(s) found.
```

# DevCon Device Console (devcon.exe)

For more information about `devcon` command please visit [Device Console Commands](https://learn.microsoft.com/en-us/windows-hardware/drivers/devtest/devcon-general-commands).

Please note that `devcon.exe` should be located in `c:\Program Files (x86)\Windows Kits\10\Tools\ARM64\` on your ARM64 machine.
See some details [here](https://learn.microsoft.com/en-us/windows-hardware/drivers/gettingstarted/writing-a-kmdf-driverbased-on-a-template#install-the-driver).

If you face issue installing driver you might look at the last sections of `c:\windows\inf\setupapi.app.log` and `c:\windows\inf\setupapi.dev.log`. They might provide some hints.

## Extra step (installing non-signed driver)

Use below steps at your own risk. 

Currently `wperf-driver` is an unsigned driver. To enable installing unsigned driver on WOA ARM64 machines requires few extra steps.

### You must first enable test-signed code

Use the following BCDEdit command line (Administrator rights required):

```
> bcdedit /set testsigning on
```

See [Enable Loading of Test Signed Drivers](https://learn.microsoft.com/en-us/windows-hardware/drivers/install/the-testsigning-boot-configuration-option#enable-or-disable-use-of-test-signed-code) article for more details.

Note: *This change takes affect after reboot*.

After reboot you can try to install driver with `DevCon Install` command.

This might further require you to:

### Second, Disable BitLocker on Windows 11

If `BitLocker` is enabled on your machine and before you attempt to disable it make sure you have `BitLocker Recover Key` for your machine.
You can obtain it in advance with procedure described [here](https://support.microsoft.com/en-us/windows/finding-your-bitlocker-recovery-key-in-windows-6b71ad27-0b89-ea08-f143-056f5ab347d6).

### Third, Disable Secure Boot on your machine

[Disable secure boot](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/disabling-secure-boot?view=windows-11) from BIOS if it has been enabled. See also [Secure boot feature signing requirements for kernel-mode drivers](https://learn.microsoft.com/en-us/windows/win32/w8cookbook/secured-boot-signing-requirements-for-kernel-mode-drivers) article for more details.
