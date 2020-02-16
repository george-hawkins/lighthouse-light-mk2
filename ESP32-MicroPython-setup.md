Adafruit HUZZAH32 initial setup and installing MicroPython
==========================================================

This page covers setting up an Adafruit HUZZAH32 - first going through the initial steps from Espressif and then through the steps needed to get MicroPython running on it.


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

The `esp-idf` pull in many submodules, e.g. [esptool](https://github.com/espressif/esptool), hence the use of `--recurse-submodules`.

Note: Espressif use `--recursive` while I used `--recurse-submodules` introduced in Git 2.13 along with `-j8` which fetches submodules in parallel.

    $ ./install.sh

This step installed various things under `~/.espressif`.

Update the `PATH` etc.:

    $ source ~/esp/esp-idf/export.sh

First project
-------------

Copy the include Hello World project:

    $ echo $IDF_PATH 
    /Users/georgehawkins/esp/esp-idf
    $ cd ~/esp
    $ cp -r $IDF_PATH/examples/get-started/hello_world .
    $ cd hello_world

While installing drivers for any kind of board may be the norm on Windows, this is one of the first for which I've had to install a driver on mac OS.

Adafruit have the necessary link to the [SiLabs CP2104 driver page](http://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers) in the "[Using with Arduino IDE](https://learn.adafruit.com/adafruit-huzzah32-esp32-feather/using-with-arduino-ide)" section of their guide to the HUZZAH32 board.

Espressif have a page on finding and working with the relevant serial port [here](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/establish-serial-connection.html).

On the Mac it's simple to find (once the necessary driver is installed and the board is connected via USB:

    $ ls /dev/cu.*
    /dev/cu.SLAB_USBtoUART ...

There's a seemingly identicall `tty` file: 

    $ ls /dev/tty.*
    /dev/tty.SLAB_USBtoUART ...

However if you use this instead the `idf.py monitor` will later complain:

    --- WARNING: Serial ports accessed as /dev/tty.* will hang gdb if launched.
    --- Using /dev/cu.SLAB_USBtoUART instead...

Note: the yellow CHG LED on the board flickers incessantly (if no battery is connected). This is apparently normal, see the end of the "[Battery + USB power](https://learn.adafruit.com/adafruit-huzzah32-esp32-feather?view=all#battery-plus-usb-power-4-2)" section of the guide.

You can configure the build setup like so:

    $ idf.py menuconfig

This displays a terminal based menu system. However this step can be skipped as the defaults are fine.

Now to build everything:

    $ idf.py build

This took some time (and involved building 910 object files).

Now to flash the result:

    $ PORT=/dev/cu.SLAB_USBtoUART
    $ idf.py -p $PORT flash

And interact with the board via the serial port monitor:

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

Let's look at that `W` line above, i.e the warning that it detected more SPI flash that wsa specified in the binary that was uploaded.

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

As per the MicroPython ESP32 [introduction](https://docs.micropython.org/en/latest/esp32/tutorial/intro.html)...

Go to the MicroPython [ESP32 firmware downloads](https://micropython.org/download#esp32) and select the GENERIC-SPIRAM firmware for ESP-IDF v4.x, i.e. the ESP32 firmware for boards, like the HUZZAH32, that have 4MB of SPI RAM, and which have been setup (as above) with ESP-IDF 4.x.

I chose the latest non-nightly (nightlies have the same name as the latest stable release with something like `-167-gf020eac6a` tagged on, the `gf020eac6a` doesn't seem to be a Git hash, perhaps it's some CI generated build number).

Once downloaded, flash it to the board:

    $ FIRMWARE=~/Downloads/esp32spiram-idf4-20191220-v1.12.bin .
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

You can then work through the MicroPython introduction to the [ESP8266](https://docs.micropython.org/en/latest/esp8266/tutorial/repl.html) as there's no separate version for the ESP32 and the two are identical for the steps covered here.
