Lighthouse light - mark 2
=========================

* [Prop-Maker FeatherWing](https://www.adafruit.com/product/3988) - US$10
* [Prop-Maker FeatherWing guide](https://learn.adafruit.com/adafruit-prop-maker-featherwing?view=all)

* [Sparkfun 1/4 Watt resistor kit](https://www.sparkfun.com/products/10969)

* [Adafruit transistors 101](https://learn.adafruit.com/transistors-101?view=all) - see FETs section and "How do I pick a transistor for the job?" section and "Driving a LED Strip" example.
* [RGB LED strips usage](https://learn.adafruit.com/rgb-led-strips?view=all#usage) - the examples here are better as they show using MOSFETs (IRLB8721) or Darlington transistors (TIP120).

* ULN2803A at [Adafruit](https://www.adafruit.com/product/970) / at [Mouser](https://www.mouser.ch/ProductDetail/511-ULN2803A)
* IRLB8721PBF [Adafruit](https://www.adafruit.com/product/355) / at [Mouser](https://www.mouser.ch/ProductDetail/942-IRLB8721PBF)

3W RGB LED hookup guide
-----------------------

Normally Adafruit has excellent hookup guides for all its parts, however all the guides for the [3W RGB LED](https://www.adafruit.com/product/2530) involve using the [Prop-Maker FeatherWing](https://www.adafruit.com/product/3988) (which includes various other features, such as an amplifier and accelerometer) designed to fit together with one of their [Feather series boards](https://www.adafruit.com/category/835). So it's not so clear how to hookup the 3W RGB LED directly to any arbitrary MCU board.

However the schematics etc. (as with all Adafruit boards) are available on GitHub. The EagleCAD files are [here](https://github.com/adafruit/Adafruit-Prop-Maker-FeatherWing-PCB). I opened the schematic in EagleCAD, converted it to an SVG with [eagle2svg](http://eagle.autodesk.com/eagle/ulp?q[title_or_author_or_description_cont]=svg) (an Eagle ULP to covert schematics to SVG) and cut out the part of the schematic that supports connecting a 3W RGB LED. The result can be seen below.

[![3W RGB LED driver](3w-rgb-led-driver.png)](3w-rgb-led-driver.svg)

* [Hi-res image of top side of Prop-Maker FeatherWing board](https://cdn-learn.adafruit.com/assets/assets/000/069/373/original/adafruit_products_PropMakerFeatherWingTop.jpg)

The Prop-Maker FeatherWing uses a very simple circuit for controlling a 3W RGB LED. The 3 wires marked red, green and blue (on the left of the diagram) should be connected to PWM pins on your MCU board, the wire labeled V+ should be connected directly to your power source, typically a 3.7V LiPo battery (like [this](https://www.adafruit.com/product/1781)) and the four pads of your 3W RGB LED should be connected to the terminal marked as JP5 (1 to the LED's + terminal, 2 to R-, 3 to G- and 4 to B-).

The circuit itself just of a 100k&ohm; pull-down resistor for each PWM input followed by a [DMG3406](https://www.mouser.ch/ProductDetail/621-DMG3406L-7) MOSFET and a limiting resistor for the particular LED color that it controls. If you look at the [datasheet](https://cdn-shop.adafruit.com/product-files/2530/FD-3RGB-Y2.pdf) for the RGB LED you'll see that the forward voltage for red is 2.5V while it's 3.6V for green and blue, hence the different limiting resistors - 3&ohm; for red and 1.75&ohm; for green and blue.

As you can see in the schematic, the pull-down resistors are marked as 0603 and the limiting resistors are marked as 1206 - this means the 100k&ohm; resistors are 1/10 Watt SMD resistors while the 3&ohm; and 1.75&ohm; resistors are 1/4 Watt SMD resistors (see [resistor sizes and packages](http://www.resistorguide.com/resistor-sizes-and-packages/)).

