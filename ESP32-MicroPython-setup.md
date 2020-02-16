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
    $ esptool.py --port $PORT --chip esp32 write_flash -z 0x1000 $FIRMWARE

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
    stack: 736 out of 15360
    GC: total: 111168, used: 5056, free: 106112
     No. of 1-blocks: 16, 2-blocks: 9, max blk sz: 264, max free sz: 6428

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

And once you're ready, you can work through the MicroPython introduction to the [ESP8266](https://docs.micropython.org/en/latest/esp8266/tutorial/repl.html) (there's no separate version for the ESP32 and the two are identical for the steps covered here).