pyOCD
=====

pyOCD is an open source Python package for programming and debugging Arm Cortex-M microcontrollers
using multiple supported types of USB debug probes. It is fully cross-platform, with support for
Linux, macOS, and Windows.

A command line tool is provided that covers most use cases, or you can make use of the Python
API to enable low-level target control. A common use for the Python API is to run and control CI
tests.

Upwards of 70 popular MCUs are supported built-in. In addition, through the use of CMSIS-Packs,
[nearly every Cortex-M device](https://www.keil.com/dd2/pack/) on the market is supported.

The `pyocd` command line tool gives you total control over your device with these subcommands:

- `gdbserver`: GDB remote server allows you to debug using gdb via either the console or
    [several GUI debugger options](#recommended-gdb-and-ide-setup).
- `flash`: Program files of various formats into flash memory.
- `erase`: Erase part or all of an MCU's flash memory.
- `pack`: Manage [CMSIS Device Family Packs](http://arm-software.github.io/CMSIS_5/Pack/html/index.html)
    that provide additional target device support.
- `commander`: Interactive REPL control and inspection of the MCU.
- `server`: Share a debug probe with a TCP/IP server.
- `reset`: Hardware or software reset of a device.
- `list`: Show connected devices.

The API and tools provide these features:

-  halt, step, resume control
-  read/write memory
-  read/write core registers
-  set/remove hardware and software breakpoints
-  set/remove watchpoints
-  write to flash memory
-  load binary, hex, or ELF files into flash
-  reset control
-  access CoreSight DP and APs
-  SWO and SWV
-  and more!

Configuration and customization is supported through [config files](docs/configuration.md),
[user scripts](docs/user_scripts.md), and the Python API.


News
----

- Python 2 support has been dropped! Version [0.29](/pyocd/pyOCD/releases/tag/v0.29.0) is the last version to support
    Python 2. If there is a critical issue on 0.29 that is a blocker for you and you cannot move to Python 2, please
    raise [an issue](https://github.com/pyocd/pyOCD/issues/new/choose).

- PyOCD has moved to [its own organization](https://github.com/pyocd/) on GitHub! You will continue to see
    further changes to increase communication and make pyOCD a more independent and open project.


Requirements
------------

- Python 3.6.0 or later.
- macOS, Linux, or Windows 7 or newer
- A recent version of [libusb](https://libusb.info/). See [libusb installation](#libusb-installation) for details.
- Microcontroller with an Arm Cortex-M CPU
- Supported debug probe
  - [CMSIS-DAP](http://www.keil.com/pack/doc/CMSIS/DAP/html/index.html) v1 (HID), such as:
    - An on-board or standalone debug probe using [DAPLink](https://os.mbed.com/handbook/DAPLink) firmware.
    - NXP LPC-LinkII
    - NXP MCU-Link
    - Atmel-ICE
  - [CMSIS-DAP](http://www.keil.com/pack/doc/CMSIS/DAP/html/index.html) v2 (WinUSB), such as:
    - [DAPLink](https://os.mbed.com/handbook/DAPLink) firmware version 0254 or newer.
    - Cypress KitProg3 or MiniProg4
    - Keil ULINKplus
  - SEGGER J-Link (experimental)
  - STLinkV2 or STLinkV3, either on-board or the standalone versions.


Status
------

PyOCD is functionally reliable and fully useable.

The Python API is considered partially unstable as we are restructuring and cleaning it up prior to
releasing version 1.0.


Documentation
-------------

The pyOCD documentation is located in [the docs directory](docs/).

In addition to user guides, you can generate reference documentation using Doxygen with the
supplied [config file](docs/Doxyfile).


Installing
----------

The latest stable version of pyOCD may be installed via [pip](https://pip.pypa.io/en/stable/index.html)
as follows:

```
$ python3 -mpip install -U pyocd
```

_Note: depending on your system, you may need to use `python` instead of `python3`._

The latest pyOCD package is available [on PyPI](https://pypi.python.org/pypi/pyOCD/) as well as
[on GitHub](https://github.com/pyocd/pyOCD/releases).

To install the latest prerelease version from the HEAD of the master branch, you can do
the following:

```
$ python3 -mpip install --pre -U git+https://github.com/pyocd/pyOCD.git
```

You can also install directly from the source by cloning the git repository and running:

```
$ python3 setup.py install
```

Note that, depending on your operating system, you may run into permissions issues running these commands.
You have a few options here:

1. Under Linux, run with `sudo -H` to install pyOCD and dependencies globally. On macOS, installing with sudo
    should never be required, although sometimes permissions can become modified such that installing without
    using sudo fails.
3. Specify the `--user` option to install local to your user account.
4. Run the command in a [virtualenv](https://virtualenv.pypa.io/en/latest/)
   local to a specific project working set.

For notes about installing and using on non-x86 systems such as Raspberry Pi, see the
[relevant documentation](docs/installing_on_non_x86.md).

### libusb installation

[pyusb](https://github.com/pyusb/pyusb) and its backend library [libusb](https://libusb.info/) are
dependencies on all supported operating systems. pyusb is a regular Python package and will be
installed along with pyOCD. However, libusb is a binary shared library that does not get installed
automatically via pip dependency management.

How to install libusb depends on your OS:

- macOS: use Homebrew: `brew install libusb`
- Linux: should already be installed.
- Windows: download libusb from [libusb.info](https://libusb.info/) and place the .dll file in your Python
  installation folder next to python.exe. Make sure to use the same 32- or 64-bit architecture as
  your Python installation. The latest release is [available on GitHub](https://github.com/libusb/libusb/releases);
  download the .7z archive under Assets. Use the library from the VS2019 folder in the archive.

### udev rules on Linux

On Linux, particularly Ubuntu 16.04+, you must configure udev rules to allow pyOCD to access debug
probes from user space. Otherwise you will need to run pyOCD as root, using sudo, which is very
highly discouraged. (You should _never_ run pyOCD as root on any OS.)

To help with this, example udev rules files are included with pyOCD in the
[udev](https://github.com/pyocd/pyOCD/tree/master/udev) folder. The
[readme](https://github.com/pyocd/pyOCD/tree/master/udev/README.md) in this folder has detailed
instructions.

### Target support

See the [target support documentation](docs/target_support.md) for information on how to check if
the MCU(s) you are using have built-in support, and how to install support for additional MCUs via
CMSIS-Packs.


Standalone GDB server
---------------------

After you install pyOCD via pip or setup.py, you will be able to execute the following in order to
start a GDB server powered by pyOCD:

```
$ pyocd gdbserver
```

You can get additional help by running ``pyocd gdbserver --help``.

Example command line GDB session showing how to connect to a running `pyocd gdbserver` and load
firmware:

```
$ arm-none-eabi-gdb application.elf

<gdb> target remote localhost:3333
<gdb> load
<gdb> monitor reset
```

The `pyocd gdbserver` subcommand is also usable as a drop in place replacement for OpenOCD in
existing setups. The primary difference is the set of gdb monitor commands.


Recommended GDB and IDE setup
-----------------------------

The recommended toolchain for embedded Arm Cortex-M development is [GNU Arm
Embedded](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm),
provided by Arm. GDB is included with this toolchain.

For [Visual Studio Code](https://code.visualstudio.com), the
[cortex-debug](https://marketplace.visualstudio.com/items?itemName=marus25.cortex-debug) plugin is available
that supports pyOCD.

The GDB server also works well with [Eclipse Embedded CDT](https://projects.eclipse.org/projects/iot.embed-cdt),
previously known as [GNU MCU/ARM Eclipse](https://gnu-mcu-eclipse.github.io/). It fully supports pyOCD with
an included pyOCD debugging plugin.

To view peripheral register values either the built-in Eclipse Embedded CDT register view can be used, or
the Embedded System Register Viewer plugin can be installed. The latter can be installed from inside
Eclipse adding `http://embsysregview.sourceforge.net/update` as a software update server URL
under the "Help -> Install New Software..." menu item.


Development setup
-----------------

Please see the [Developers' Guide](docs/developers_guide.md) for instructions on how to set up a
development environment for pyOCD.


Contributions
-------------

We welcome contributions to pyOCD in any area. Please see the [contribution
guidelines](CONTRIBUTING.md) for detailed requirements for contributions. In order foster a healthy
and safe community, we expect contributors to follow the [code of conduct](CODE_OF_CONDUCT.md).

To report bugs, please [create an issue](https://github.com/pyocd/pyOCD/issues/new) in the
GitHub project.


License
-------

PyOCD is licensed with the permissive Apache 2.0 license. See the [LICENSE](LICENSE) file for the
full text of the license.

Copyright © 2006-2021 The PyOCD authors
