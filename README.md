3W RGB LED hookup guide
=======================

Normally Adafruit has excellent hookup guides for all its parts, however all the guides for the [3W RGB LED](https://www.adafruit.com/product/2530) involve using the [Prop-Maker FeatherWing](https://www.adafruit.com/product/3988) (which includes various other features, such as an amplifier and accelerometer). This "Wing" is designed to fit together with one of their [Feather series boards](https://www.adafruit.com/category/835). So it's not so clear how to hookup the 3W RGB LED directly to any arbitrary MCU board.

However the schematics etc. (as with all Adafruit boards) are available on GitHub. The EagleCAD files are [here](https://github.com/adafruit/Adafruit-Prop-Maker-FeatherWing-PCB). I opened the schematic in EagleCAD, converted it to an SVG with [eagle2svg](http://eagle.autodesk.com/eagle/ulp?q[title_or_author_or_description_cont]=svg) (an Eagle ULP to covert schematics to SVG) and cut out the part of the schematic that supports connecting a 3W RGB LED. The result can be seen below.

[![3W RGB LED driver schematic](3w-rgb-led-driver.png)](3w-rgb-led-driver.svg)

The Prop-Maker FeatherWing uses a very simple circuit for controlling a 3W RGB LED. The 3 wires marked red, green and blue (on the left of the diagram) should be connected to PWM pins on your MCU board, the wire labeled V+ should be connected directly to your power source, typically a 3.7V LiPo battery (like [this](https://www.adafruit.com/product/1781)) and the four pads of your 3W RGB LED should be connected to the terminal marked as JP5 (1 to the LED's + terminal, 2 to R-, 3 to G- and 4 to B-).

The circuit itself just of a 100k&ohm; pull-down resistor for each PWM input followed by a [DMG3406](https://www.mouser.ch/ProductDetail/621-DMG3406L-7) MOSFET and a limiting resistor for the particular LED color that it controls. If you look at the [datasheet](https://cdn-shop.adafruit.com/product-files/2530/FD-3RGB-Y2.pdf) for the RGB LED you'll see that the forward voltage for red is 2.5V while it's 3.6V for green and blue, hence the different limiting resistors - 3&ohm; for red and 1.75&ohm; for green and blue.

As you can see in the schematic, the pull-down resistors are marked as 0603 and the limiting resistors are marked as 1206 - this means the 100k&ohm; resistors are 1/10 Watt SMD resistors while the 3&ohm; and 1.75&ohm; resistors are 1/4 Watt SMD resistors (see [resistor sizes and packages](http://www.resistorguide.com/resistor-sizes-and-packages/)).

If you look at the [Prop-Maker FeatherWing guide](https://learn.adafruit.com/adafruit-prop-maker-featherwing?view=all) you can clearly see this circuit in the guide's hi-resolution image of the top of the board:

[<img height="512" src="propmaker-3w-rgb-led-driver.png">](https://cdn-learn.adafruit.com/assets/assets/000/069/373/original/adafruit_products_PropMakerFeatherWingTop.jpg)

The pull-down resistors are to the left, then the three pin MOSFETs, then the larger limiting resistors and finally pads for connecting the 3W RGB LED.

If you look at the [complete schematic](https://learn.adafruit.com/assets/69243) and the [logic pins section](https://learn.adafruit.com/adafruit-prop-maker-featherwing?view=all#logic-pins-2-5) of the guide, it's clear that you just connect the PWM inputs directly to pins on your MCU while V+ is connected either directly to your battery or to a pin on your MCU that provides the battery voltage directly (you should **not** attempt to power it off one of the logic pins on your MCU board, these can typically supply at most 40mA and will burn out trying to power a circuit like this).

Creating the 3W LED driver circuit with through-hole components
---------------------------------------------------------------

So as we can see the necessary circuit is very simple. Now let's look at how to reproduce it on a breadboard.

The following two Adafruit guides provided all the information needed to produce the circuit below:

* [Adafruit transistors 101](https://learn.adafruit.com/transistors-101?view=all) - in particular see the FETs section, the "How do I pick a transistor for the job?" section and the "Driving a LED Strip" example (using Darlington transistors).
* The usage section of the [RGB LED strips guide](https://learn.adafruit.com/rgb-led-strips?view=all#usage) - the examples here are better as they show using MOSFETs ([IRLB8721](https://www.adafruit.com/product/355)) as well as Darlington transistors ([TIP120](https://www.adafruit.com/product/976)).

<img width="720" src="3w-rgb-led-circuit_bb.png">

The [Fritzing](https://fritzing.org/home/) file for this circuit is [here](3w-rgb-led-circuit.fzz).

Note: in the diagram above the 3W RGB LED is shown as if it were a breadboardable component (with pins on the underside corresponding to the pads seen on the top). In reality you'd have to solder the resistors directly to each pad (or solder on jumper wires).

This circuit uses a 3.7V battery. This [Adafruit forum post](https://forums.adafruit.com/viewtopic.php?f=47&t=79083) covers using a 5V source and lists the different limiting resistors needed when using 5V (rather than 3.7V).

TODO: that post says you should use a 1W resistor for the red and 1/2W resistors for green and blue. So 5V is obviously higher than 3.7V but still this is quite a step up from the 1/4W resistors used on the Prop-Maker FeatherWing. Do you really need such high Wattage resistors and do I need higher than 1/4W resistors for my breadboard layout?

TODO: this circuit is essentially the same as the MOSFET example in the [RGB LED strips guide](https://learn.adafruit.com/rgb-led-strips?view=all#usage). Like that example, I don't use pull-down resistors on the PWM pins. Why does the Prop-Maker FeatherWing include them?

Alternatives
------------

The 3W RGB LED is US$3 and the MOSFETs and resistors add additional cost (the MOSFETs are $1.75 each from Adafruit but considerably cheaper at $0.85 from e.g. [Digikey](https://www.digikey.com/products/en?keywords=IRLB8721PBF) or [Mouser](https://www.mouser.com/ProductDetail/942-IRLB8721PBF)). And the resistors will have to be ordered from somewhere like Mourser or Digikey as these low Ohm resistors aren't carried by hobbyist sites like Adafruit or Sparkfun.

So this comes to a minimum of $3 + (3 x $0.85) + (3 x $0.10) = $5.85 (not including postage). You're not far off the $10 cost of the Prop-Maker FeatherWing (especially when you factor in your time and effort as a cost).

Another alternative is the [Adafruit Pixie](https://www.adafruit.com/product/2741) a 3W smart pixel with all necessary components built-in. At $15, it's considerably more expensive than building the circuit above but you save assembly time and the Pixie has additional useful features such as temperature cut-off.

---

* [1/4W 1.8&ohm; resistor](https://www.digikey.com/product-detail/en/yageo/CFR-25JB-52-1R8/1.8QBK-ND/154) - $0.10 - 1.8&ohm; is as close as you can get to 1.75&ohm; for through-hole resistors.
* [1/4W 3&ohm; resistor](https://www.digikey.com/product-detail/en/yageo/CFR-25JB-52-3R/3.0QBK-ND/1440) - $0.10

---

Lighthouse light - mark 2
=========================

* [Sparkfun 1/4 Watt resistor kit](https://www.sparkfun.com/products/10969)

* ULN2803A at [Adafruit](https://www.adafruit.com/product/970) / at [Mouser](https://www.mouser.ch/ProductDetail/511-ULN2803A)

Are these 3W RGB LEDs really that bright?
-----------------------------------------

So how do these 3W RGB LEDs compare with the brightest of the usual through-hole LEDs? It turns out it all depends what you mean by bright!

If you look at the Adafruit product description for their 3W RGB LED you'll see:

> Each of the red, green, and blue dice can handle continuous ~350mA current draw (with peaks of 1000mA). The light output is 110 lumens (110,000 millicandela) total.

So the combined current draw of the three colors is around 1A and the total light output is about 110lm which Adafruit say is equivalent to 110cd.

However converting from lumens to candela isn't a simple one-to-one conversion as Adafruit seem to suggest. It depends on what's called the apex angle and the conversion is only one-to-one if the apex angle is ~65.5&deg;. However according to the LED's datasheet its apex angle is 120&deg; - so 110lm and an apex angle of 120&deg; converts to just 35cd (see the RapidTables [lumens-to-candela calculator](https://www.rapidtables.com/calc/light/lumen-to-candela-calculator.html)).

If you then look at the product page for the Adafruit [super-bright white 5mm LED](https://www.adafruit.com/product/754) you can see:

> * 15,000 mcd minimum brightness
> * Viewing Angle : +/-12 degrees
> * Maximum continuous current: 20 mA

They say you get 15cd minimum _brightness_ and that for just 20mA.  So it seems like just 3 of these will achieve more brightness than the 3W RGB LED and for only 60mA in total (which an MCU board can deliver directly without requiring MOSFETs etc.).

So what's going on here? It turns out that when engineers talk about lumens and candella, they're talking about intensity and light output in a far more precise manner than when people use the word "brightness" in everyday language. For us, it's generally not really useful to compare one candela value with another if you don't also talk about the apex angle. If you're just interested in the total amount of light output by the LED then it's more useful to talk in terms of lumens. So instead of converting things to candela, as above, let's convert to lumens.

The 15cd and an apex angle of 24&deg; (2 x 12&deg;) for the super-bright white 5mm LED, converts to just 2lm (see the RapidTables [candela-to-lumens calculator](https://www.rapidtables.com/calc/light/candela-to-lumen-calculator.html)).

So the total light output of the 3W RGB LED, with its 110lm, is 55 times that of the brightest of Adafruit's through hole LED - that sounds a bit more impressive.

For much more on the relationship between candela and lumens see CompuPhase's [Candela, Lumen, Lux: the equations](https://www.compuphase.com/electronics/candela_lumen.htm). In short you can sum it up as follows. The total light output of a laser pointer is fairly low but the light it does output is very focused - the intensity of this light is very high but it just lights up a tiny point. So a laser pointer has a very high candela value but a low lumens value. In contrast a light bulb outputs a lot of light but in all directions (unlike the focused beam of a laser pointer) so it lights up a whole room but the light isn't very intense. So a light bulb has a far higher lumens value but a relatively low candela value.

If you look at Mouser or Digikey you can find 20mA white LEDs (like [this](https://www.mouser.ch/ProductDetail/941-C503DWANCCBEB152)) that go up to 48cd and red LEDs, which for whatever reason are produced with much higher candela values than blue or green LEDs, (like [this](https://www.mouser.ch/ProductDetail/78-VLCS5830)) that go up to 65cd. However if you look at such products you'll see that their apex angles are tiny. For standard 20mA LEDs the higher the candela value the lower the apex angle and once you take this into account you can see that all such LEDs tend to produce much the same light output - around 1 or 2lm.

TODO: it seems to me that viewing angle and apex angle are equivalent - is this definitely correct?

