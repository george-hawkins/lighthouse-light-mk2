Adafruit HUZZAH32 setup for MicroPython
=======================================

This page covers setting up an [Adafruit HUZZAH32](https://www.adafruit.com/product/3405) for [MicroPython](https://www.micropython.org/). The initial steps involve installing the latest Espressif IoT Development Framework (ESP-IDF) and, once this is done, MicroPython is installed.


Basic setup
-----------

Install the [prerequisites](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/#step-1-install-prerequisites). For Mac this involved:

    $ sudo easy_install pip
    $ pip install --user pyserial
    $ brew install cmake ninja

Then just work through the Espressif "[Get ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/#step-2-get-esp-idf)" steps:

    $ mkdir ~/esp
    $ cd ~/esp
    $ git clone --recurse-submodules -j8 git@github.com:espressif/esp-idf.git

`esp-idf` pulls in many submodules, e.g. [esptool](https://github.com/espressif/esptool), hence the use of `--recurse-submodules`.

Note: Espressif use `--recursive` while I used `--recurse-submodules`, introduced in Git 2.13, along with `-j8` which fetches submodules in parallel.

Then after cloning:

    $ cd esp-idf
    $ ./install.sh

This step installs various things under `~/.espressif`.

And finally update the `PATH` etc.:

    $ source ~/esp/esp-idf/export.sh

This last step needs to be added to your `~/.bashrc` or repeated each time you open a new terminal.

First project
-------------

Copy the included Hello World project:

    $ echo $IDF_PATH 
    /Users/georgehawkins/esp/esp-idf
    $ cd ~/esp
    $ cp -r $IDF_PATH/examples/get-started/hello_world .
    $ cd hello_world

You can now configure the build setup like so:

    $ idf.py menuconfig

This displays a terminal based menu system. However this step can be skipped as the defaults are fine.

Now to build everything:

    $ idf.py build

This takes some time (and involves building 910 object files).

Once built, the board needs to be plugged in and the serial port, that corresponds to the board, needs to be determined.

But before you connect the board via USB you need to install a driver. This may be the norm for any board when using Window, but this is one of the first for which I've had to do this on mac OS. Adafruit have the necessary link to the [SiLabs CP2104 driver page](http://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers) in the "[Using with Arduino IDE](https://learn.adafruit.com/adafruit-huzzah32-esp32-feather/using-with-arduino-ide)" section of their HUZZAH32 guide.

Once the appropriate driver is installed and the board is connected, follow the Espressif [instructions](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/establish-serial-connection.html) on how to determine the board's serial port. On the Mac it's simple:

    $ ls /dev/cu.*
    /dev/cu.SLAB_USBtoUART ...

There's a seemingly identicall `tty` file: 

    $ ls /dev/tty.*
    /dev/tty.SLAB_USBtoUART ...

However if you use this the `idf.py monitor` will later complain:

    --- WARNING: Serial ports accessed as /dev/tty.* will hang gdb if launched.
    --- Using /dev/cu.SLAB_USBtoUART instead...

Note: when connected via USB, the yellow CHG LED on the board flickers incessantly (if no battery is connected). This is apparently normal, see the end of the "[Battery + USB power](https://learn.adafruit.com/adafruit-huzzah32-esp32-feather?view=all#battery-plus-usb-power-4-2)" section of the Adafruit guide.

Now to flash the result of the build process to the board:

    $ PORT=/dev/cu.SLAB_USBtoUART
    $ idf.py -p $PORT flash

And then interact with the board via the serial port monitor:

    $ idf.py -p $PORT monitor

Various bits of interesting boot information flash past:

    I (29) boot: ESP-IDF v4.1-dev-2263-g605da33c3 2nd stage bootloader
    I (29) boot: chip revision: 1
    I (40) boot.esp32: SPI Speed      : 40MHz
    I (45) boot.esp32: SPI Mode       : DIO
    I (49) boot.esp32: SPI Flash Size : 2MB
    ...
    I (195) cpu_start: Application information:
    I (200) cpu_start: Project name:     hello-world
    ...
    I (210) cpu_start: Compile time:     Feb 15 2020 22:28:28
    ...
    I (239) heap_init: Initializing. RAM available for dynamic allocation:
    I (246) heap_init: At 3FFAE6E0 len 00001920 (6 KiB): DRAM
    I (252) heap_init: At 3FFB2968 len 0002D698 (181 KiB): DRAM
    I (258) heap_init: At 3FFE0440 len 00003AE0 (14 KiB): D/IRAM
    I (264) heap_init: At 3FFE4350 len 0001BCB0 (111 KiB): D/IRAM
    I (271) heap_init: At 40089994 len 0001666C (89 KiB): IRAM
    ...
    W (296) spi_flash: Detected size(4096k) larger than the size in the binary image header(2048k). Using the size in the binary image header.

And then the output from the program:

    Hello world!
    This is esp32 chip with 2 CPU cores, WiFi/BT/BLE, silicon revision 1, 2MB external flash
    Restarting in 10 seconds...
    ...
    Restarting now.

So all it does is print 'Hello world!' and some basic information about the chip and any external flash - and then restarts the board.

You can exit the monitor with `ctrl-[`.

Let's look at that `W` line above, i.e the warning that it detected more SPI flash that was specified in the binary that was uploaded.

Start the menu:

    $ idf.py menuconfig

And go to `Serial flasher config` / `Flash size` and select `4 MB` rather than the `2 MB` default. Then `Q` (exit) and save. This updates the `sdkconfig` file:

    $ diff sdkconfig sdkconfig.old 
    82,83c82,83
    < CONFIG_ESPTOOLPY_FLASHSIZE_4MB=y
    ---
    > CONFIG_ESPTOOLPY_FLASHSIZE_2MB=y
    86c86
    < CONFIG_ESPTOOLPY_FLASHSIZE="4MB"
    ---
    > CONFIG_ESPTOOLPY_FLASHSIZE="2MB"
    ...

Now rebuild, reupload and check the monitor output:

    $ idf.py build
    $ idf.py -p $PORT flash
    $ idf.py -p $PORT monitor

Now the `W` no longer appears and the Hello World program outputs the correct amount of flash:

    Hello world!
    This is esp32 chip with 2 CPU cores, WiFi/BT/BLE, silicon revision 1, 4MB external flash

### Linux notes

On Linux, unlike Mac, I did not have to install an SiLabs CP2104 driver. I could immediately access the board via `/dev/ttyUSB0` without any extra configuration.

If you're already a member of the group that can access serial devices, `dialout` on Ubuntu, no additional setup is needed. But if you'd like permissions on the device to be automatically set so that all users can access it and access via a fixed name then you can add this rule to `/etc/udev/rules.d/50-serial-ports.rules`:

    # Silicon Labs Product: CP2104 USB to UART Bridge Controller (used by Adafruit HUZZAH32).
    SUBSYSTEM=="tty", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", \
        SYMLINK+="cp2104", MODE="0666"

With this rule I can access the board like so:

    $ PORT=/dev/cp2104
    $ screen $PORT 115200

Note: `screen` behaves differently on Mac and Linux. On Mac quiting requires pressing `ctrl-a` an then `ctrl-\`, while on Linux it requires `ctrl-a` and then just `\`.

Updating ESP-IDF
----------------

As noted in the Espressif instructions for [updating your setup](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/#updating-esp-idf), there are no special steps - you simply throw away your existing `esp-idf` directory and go through the above steps again.

Installing MicroPython
----------------------

The following is just a condensed form of the MicroPython ESP32 [introduction](https://docs.micropython.org/en/latest/esp32/tutorial/intro.html).

Go to the MicroPython [ESP32 firmware downloads](https://micropython.org/download#esp32) and select the GENERIC firmware for ESP-IDF v4.x, i.e. the ESP32 firmware for boards, like the HUZZAH32, that have no external SPI RAM, and which have been setup (as above) with ESP-IDF 4.x.

**Important:** I initially used the GENERIC-SPIRAM firmware, which is intended for boards that 4MB of external pSRAM. The Adafruit [product page](https://www.adafruit.com/product/3405) notes that the board has "4 MB of SPI Flash", however pSRAM is something different (as explained [here](https://docs.espressif.com/projects/esp-idf/en/latest/api-guides/external-ram.html#hardware)). If you use the GENERIC-SPIRAM, it still works fine but you see these errors in the boot sequence:

    E (593) spiram: SPI RAM enabled but initialization failed. Bailing out.
    E (10) spiram: SPI RAM not initialized

I chose the latest non-nightly (nightlies have the same name as the latest stable release but with something like `-167-gf020eac6a` tagged on, the `gf020eac6a` doesn't seem to be a Git hash, perhaps it's some CI generated build number).

Once downloaded, flash it to the board:

    $ FIRMWARE=~/Downloads/esp32-idf4-20191220-v1.12.bin
    $ esptool.py --port $PORT erase_flash
    $ esptool.py --port $PORT write_flash -z 0x1000 $FIRMWARE

Note: many examples includes the the argument `--chip esp32`, however `esptool.py` now automatically detects the chip version.

Once uploaded you can connect to the MicroPython REPL:

    $ screen $PORT 115200

Just press return to get a prompt and then:

    >>> help()
    Welcome to MicroPython on the ESP32!
    ...
    For access to the hardware use the 'machine' module:
    ...
    import machine
    pin12 = machine.Pin(12, machine.Pin.OUT)
    ...
    Basic WiFi configuration:
    ...
    import network
    sta_if = network.WLAN(network.STA_IF);
    ...

Then press the reset button on the board - you'll see a boot sequence similar to the one seen above when working with the Hello World example:

    I (519) cpu_start: Pro cpu up.
    I (519) cpu_start: Application information:
    I (519) cpu_start: Compile time:     Dec 20 2019 07:56:38
    I (522) cpu_start: ELF file SHA256:  0000000000000000...
    I (528) cpu_start: ESP-IDF:          v4.0-beta1
    I (533) cpu_start: Starting app cpu, entry point is 0x40083014
    I (526) cpu_start: App cpu up.

You can confirm that it's found the 4MB of flash RAM:

    >>> import esp
    >>> esp.flash_size()
    4194304

The flash is assigned to a virtual filesystem, while the onboard RAM of the ESP32 is split between heap (managed by the GC) and stack:

    >>> import os
    >>> os.statvfs('/')
    (4096, 4096, 506, 505, 505, 0, 0, 0, 0, 255)
    >>> import micropython
    >>> micropython.mem_info()
    stack: 736 out of 15360
    GC: total: 111168, used: 104224, free: 6944
     No. of 1-blocks: 2172, 2-blocks: 381, max blk sz: 264, max free sz: 196
    >>> import gc
    >>> gc.collect()
    >>> micropython.mem_info()
    stack: 736 out of 15360
    GC: total: 111168, used: 83200, free: 27968
     No. of 1-blocks: 1175, 2-blocks: 226, max blk sz: 264, max free sz: 119

You can take a look at the filesystem like so:

    >>> import os
    >>> os.listdir('/')
    ['boot.py']
    >>> f = open('boot.py')
    >>> f.read()
    '# This file is executed on every boot ...'
    >>> f.close()

Once you've had a look around, try turning on the red LED that's next to the USB port and connected to GPIO #13:

    >>> import machine
    >>> pin13 = machine.Pin(13, machine.Pin.OUT)
    >>> pin13.value(1)

And then turn it off:

    >>> pin13.value(0)

TODO: connect an external basic red LED to one of the pins, in series with a resistor that restricts the current to 10mA (it looks like the ESP32 can definitely source at least 12mA per pin and up to 40mA, with certain constraints, but it seems better to stick to a definitely safe value even if it means the LED is a bit dim).

The REPL supports auto-indent which is useful when entering larger pieces of code, however if you're copying and pasting in an already properly indented piece of code, the auto-indent feature will end up over indenting everything. To deal with this you need to use the REPL's [paste mode](http://docs.micropython.org/en/latest/esp8266/tutorial/repl.html#paste-mode) - just press `ctrl-E` to enter paste mode, then paste in the required text and press `ctrl-D` to exit paste mode.

You can discover more about the available modules like so:

    >>> help("modules")
    __main__          gc                uctypes           urequests
    ...
    framebuf          ucryptolib        ure
    Plus any modules on the filesystem
    >>> import machine
    >>> dir(machine)
    [... 'DEEPSLEEP', 'DEEPSLEEP_RESET', ..., 'sleep', 'time_pulse_us', 'unique_id', 'wake_reason']

Note: a lot of the modules have names like `uos`, `uio` etc., i.e. names that start with `u`. The `u` indicates a micro version of a standard Python module. You should drop the `u` when importing such modules, e.g. `import os`, i.e. use the standard module name (although it's doesn't do any harm not to). For more, see [this post](https://forum.micropython.org/viewtopic.php?p=40415#p40415) on the MicroPython forums.

You can find the documentation for the standard libraries and the MicroPython-specific libraries [here](https://docs.micropython.org/en/latest/library/index.html#python-standard-libraries-and-micro-libraries).

Once you're ready, you can work through the MicroPython tutorial for the [ESP8266](https://docs.micropython.org/en/latest/esp8266/tutorial/repl.html) (there's no separate version for the ESP32 and the two are identical for things covered here). And after that you can return to the ESP32 specific [quick reference](http://docs.micropython.org/en/latest/esp32/quickref.html).

Working with the MicroPython filesystem
---------------------------------------

As seen above the filesystem of a newly setup board just contains a single file called `boot.py`. You generally leave this as it is and just upload the main script, that you want the board to run, as `main.py` (for more on this see the Adafruit [guide](https://learn.adafruit.com/micropython-basics-load-files-and-run-code/boot-scripts) to the boot scripts). If you need any additional modules, beyond the default provided ones, you'll also need to upload these.

A little surprisingly there's no standard MicroPython tool for doing this. There are a number of third part tools available - one of the simplest and most popular is [Ampy](https://github.com/scientifichackers/ampy) which I'll use here.

Note: MicroPython doesn't currently support any mechanism to interact with the filesystem other than via the REPL, so under the covers tools like Ampy just interact with the REPL like a human does rather than having a proper API for interacting with the filesystem. This makes things a bit hacky, e.g see issue [#64](https://github.com/scientifichackers/ampy/issues/64).

Before you install anything Python related you should be operating in a Python virtual environment. If you haven't this setup already, you can do it like so:

    $ brew install python
    $ python3 -m venv ~/esp-env
    $ source ~/esp-env/bin/activate
    $ pip install --upgrade pip

The `source` step is the only one you need to repeat, if you open a new terminal and need to activate the virtual environment again. Once an environment is activated you can just use commands like `python` and `pip` rather than worrying about using specific versions like `python3` or `pip3`.

Now to install Ampy itself:

    $ pip install git+https://github.com/scientifichackers/ampy.git
    $ ampy --help
    Usage: ampy ...

Using the package name `git+https://github.com/scientifichackers/ampy.git` means that you get the latest version available on GitHub. Ampy is under active development but they seem to have stopped making releases to PyPi in October 2018 (see the PyPi [release history](https://pypi.org/project/adafruit-ampy/#history)).

Assuming your board is connected and you've still got the `PORT` environment variable set you can start using Ampy like so:

    $ export AMPY_PORT=$PORT
    $ ampy ls
    /boot.py
    $ ampy get /boot.py
    # This file is executed on every boot (including wake-boot from deepsleep)
    ...

Note that `get` just displays the contents of the file, it doesn't copy the file to your machine. Now let's upload a script:

    $ cat > main.py << EOF
    import machine
    import time

    pin13 = machine.Pin(13, machine.Pin.OUT)
    on = 1 
    while True:
        pin13.value(on)
        on ^= 1
        time.sleep(0.2)
    EOF
    $ ampy put main.py

Now press the reset button on the board and the script will start running.

Note: if you get the error message `Could not enter raw repl` when using Ampy, you'll have to set `AMPY_DELAY` (see the [configuration section](https://github.com/scientifichackers/ampy#configuration) of the Ampy `README`).

### An alternative

MicroPython do actually provide a tool as well, called [`pyboard.py`](https://docs.micropython.org/en/latest/reference/pyboard.py.html), but you have to do some hand assembly. Assuming you've already got a Python virtual environment setup as above, just do:

    $ curl -O https://raw.githubusercontent.com/micropython/micropython/master/tools/pyboard.py
    $ chmod a+x pyboard.py 
    $ pip install pyserial

Now it's setup, you can use it like so:

    $ ./pyboard.py --device $PORT -f ls
    ls :
         139 boot.py
         141 main.py
    $ ./pyboard.py --device $PORT -f cat :main.py
    cat :main.py
    import machine
    import time
    ...

Note the `:` before `main.py`, the colon means the remote device rather than the local machine. So you can copy a file to the board like this:

    $ ./pyboard.py --device $PORT -f cp main.py :main.py

Or just:

    $ ./pyboard.py --device $PORT -f cp main.py :

For more details see the [`pyboard.py` documentation](https://docs.micropython.org/en/latest/reference/pyboard.py.html).

### Another alternative - rshell

[Rshell](https://github.com/dhylands/rshell) is developed by Dave Hylands. Unlike `ampy` and `pyboard.py` it provides a shell like environment that includes access to the MicroPython REPL. This avoids having to continuously switch between a tool like `screen` to interact with REPL and another tool to upload files to the board.

You can install it like so:

    $ pip install rshell

A nice feature of `rshell` is that you can ask it to discover the serial port of your board:

    $ rshell --list
    USB Serial Device 10c4:ea60 with vendor 'Silicon Labs' serial '01D1884D' found @/dev/ttyUSB0

Aside: I have a udev rule that means I know by board is always available via the soft link `/dev/cp2104`.

Once installed, you can start `rshell` like so:

    $ rshell -p /dev/cp2104
    Using buffer-size of 32
    Connecting to /dev/cp2104 (buffer-size 32)...
    Trying to connect to REPL  connected
    Testing if sys.stdin.buffer exists ... Y
    Retrieving root directories ...
    Setting time ... Mar 07, 2020 16:22:55
    Evaluating board_name ... pyboard
    Retrieving time epoch ... Jan 01, 2000 

The important thing displayed as part of connecting is the name of your board, here it's `pyboard`. You have to use this name whenever you want to refer to the board's file system rather than your local filesystem.

    > ls -l /pyboard
       139 Jan  1 2000  boot.py
      5253 Jan  1 2000  main.py
    > repl
    Entering REPL. Use Control-X to exit.
    MicroPython v1.12 on 2019-12-20; ESP32 module with ESP32
    Type "help()" for more information.
    >>>

As it says, use `ctrl-X` to exit and return to the `rshell` prompt. When you run `repl` for the first time, it seems to actively interrupt any running program, in order to get you to the REPL prompt, but if you exit to the `rshell` prompt then it doesn't do this on subsequent invocations of `repl`.

Tab completion works for filenames in various situations, e.g. when using `ls`, but does not work in others, e.g. when using the `connect` command.

When I start `rshell` using `-p` it connects with a buffer size of 32:

    $ rshell -p /dev/cp2104
    Using buffer-size of 32
    Connecting to /dev/cp2104 (buffer-size 32)...

However the `README` for `rshell` says that the default buffer size is 512. If I connect within `rshell` then it does use this buffer size:

    $ rshell
    > connect serial /dev/cp2104
    Connecting to /dev/cp2104 (buffer-size 512)...

I don't know why it defaults to a smaller buffer size in the first situation but it makes a huge difference to file transfer speeds. So when using `-p` you should specify the buffer size explicitly:

    $ rshell -p /dev/cp2104 --buffer-size 512
    Using buffer-size of 512
    Connecting to /dev/cp2104 (buffer-size 512)...
    ...
    > cp my-large-file /pyboard

Letting it use a buffer size of 32 results in file transfer that are 3 times slower for large files. I experimented with other buffer sizes - there's no further gain from increasing the buffer size beyond 512.

If you unplug your board and then reconnect it, `rshell` automatically reconnects. However, I found this didn't work perfectly, e.g. after reconnection I found that pressing `ctrl-C` while in the MicroPython REPL killed `rshell` rather than simply interrupting whatever was running in MicroPython.

For simply doing a hard reset, pressing the reset button on the board works fine. Or in the REPL one can do the following:

    > repl
    >>> import machine; machine.reset()

It's nice that `rshell` behaves as if `/pyboard` is a directory of your local filesystem, i.e. it pretends that your board is mounted under this directory. However the disadvantage of not having the concept of a local and remote filesystem is that you end up having to use long-ish path names to refer to your _local_ files if you've changed directory within `rshell` to `/pyboard`:

    > cd /pyboard
    > ls
    boot.py
    > cp ~/my-project/main.py .
    > ls 
    boot.py        main.py
    
Aside: `rshell` understands that `~` means your home directory but currently tab completion breaks when using `~`.

Note: when you use `ls` wihtout `-l` it doesn't behave quite like normal `ls`, it groups files by type (directories, `.py` files etc.) and sorts the filenames within these groups.

You can also use `rshell` to run a single command and then exit:

    $ rshell -p /dev/cp2104 ls /pyboard
    rshell -p /dev/cp2104 ls /pyboard
    Using buffer-size of 32
    ...
    Retrieving time epoch ... Jan 01, 2000
    boot.py        main.py

Use `--quiet` if you don't want all the start-up output:

    $ rshell -p /dev/cp2104 --quiet ls /pyboard
    boot.py        main.py

You can also use this to go straight into the REPL:

    $ rshell -p /dev/cp2104 --quiet repl
    ...
    >>>

You can get `rshell` to run things in the REPL and then exit:

    $ rshell -p /dev/cp2104 --quiet repl '~ import sys ~ sys.implementation ~'
    >>> import sys ; sys.implementation
    (name='micropython', version=(1, 12, 0), mpy=10757)

Note that you have to use `~` (tilde) instead of `;` to separate statements. The first tilde is not optional but if you leave out the last tilde then `rshell` stays in the REPL rather than exiting and returning to your normal prompt.

You can also include a sequence of `rshell` commands in a script:

    $ cat > myscript << EOF
    connect serial /dev/cp2104
    rm -r /pyboard/libs
    cp -r libs /pyboard
    EOF
    $ rshell --quiet -f myscript 
    ...

Notes:

* Rshell does not save your command history, so if you exit and restart `rshell` you can't just search backwards for what you were doing previously.
* Rshell development seems to have stopped in mid-2019, Peter Hinch [forked it](https://github.com/peterhinch/rshell) and added a macro feature (similar to `alias` in Bash) but does not seem to be developing it further either.

### Yet another alternative - mpfshell

Currently `pyboard.py`, `ampy`, `rshell` and `mpfshell` seem to be the main tools people are using to interact with MicroPython boards.

So let's install and take a look at the last of these - [`mpfshell`](https://github.com/wendlers/mpfshell):

    $ pip install mpfshell

Note that `mpfshell`, like `ampy` and `rshell`, is currently not being very actively developed.

You have to leave out the `/dev` to connect to particular device:

    $ mpfshell cp2104 
    Connected to esp32

    ** Micropython File Shell v0.9.1, sw@kaltpost.de ** 
    -- Running on Python 3.5 using PySerial 3.4 --

    mpfs [/]> help

    Documented commands (type help <topic>):
    ========================================
    EOF  cd     exec  get   lcd  lpwd  md    mput  mrm   put   pwd   rm
    cat  close  exit  help  lls  ls    mget  mpyc  open  putc  repl

It's feature set is very similar to that of `rshell`.

An interesting additional feature is the ability to compile files on the fly and copy the resulting `.mpy` file to your board using `putc`:

    mpfs [/]> putc main.py
    mpfs [/]> ls
    Remote files in '/':
           main.mpy 

This depends on `mpy-cross` being present in your path (see the section on precompiling MicroPython files):


Precompiling MicroPython files
------------------------------

Python is an interpreted language and normally there's no separate compile phase. The same is true when using MicroPython.

MicroPython actually compiles your `.py` files on-the-fly as needed on your board. Normally this works fine but when memory is tight the compiler itself may run out of memory.

You can avoid this by pre-compiling your `.py` files to `.mpy` files and then copying these, rather than the original `.py` files, to your board.

For more about `.mpy` files see the [MicroPython documentation](https://docs.micropython.org/en/latest/reference/mpyfiles.html).

Pre-compiling depends on the MicroPython [`mpy-cross`](https://github.com/micropython/micropython/tree/master/mpy-cross) tool which you can build from source yourself.

An easier alternative is to use the version made available [via PyPi](https://pypi.org/project/mpy-cross/).

Note that this version isn't made available directly by the Micropython GitHub organization. Instead it's built by the [mpy_cross](https://gitlab.com/alelec/mpy_cross) project on GitLab that's maintained by Andrew Leech (who also contributes commits to the main Micropython repo). His project is basically a CI setup that imports Micropython as a submodule (which is updated weeky to point to the latest commit) and creates builds for Windows, Mac and Linux. As such, it's much simpler to use than building from source yourself.

    $ pip install mpy_cross
    
For some reason the executable isn't automatically copied/linked to the `bin` directory of your current environment, so find the package location and then the executable:

    $ pip show mpy_cross
    ...
    Location: .../env/lib/python3.5/site-packages
    ...
    $ ls .../env/lib/python3.5/site-packages/mpy_cross
    ... mpy-cross

And create the necessary link yourself and use `mpy-cross` like any other command:

    $ ln -s .../env/lib/python3.5/site-packages/mpy_cross/mpy-cross .../env/bin
    $ mpy-cross --version
    MicroPython v1.12 on 2019-12-21; mpy-cross emitting mpy v5

Note: make sure to use an absolute path when specifying the source of the soft link.

I've filed issue [#8](https://gitlab.com/alelec/mpy_cross/-/issues/8) to ask why this link isn't done automatically.

Now you can compile files locally and copy them to your board:

    $ mpy-cross main.py 
    $ ls main.*
    main.mpy  main.py
    pyboard.py --device $PORT -f cp main.mpy :

As well as avoiding having to do compilation work on your board, the resulting `.mpy` files are significantly smaller than the original `.py` files.

