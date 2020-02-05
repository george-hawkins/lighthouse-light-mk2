Parts
=====

### 3W RGB common anode LED hookup

| Description | Quantity | Adafruit | Mouser | Digikey |
|-------------|----------|----------|--------|---------|
| 3W RGB common anode LED | 2 | [$3](https://www.adafruit.com/product/2530) | [$3](https://www.mouser.com/ProductDetail/485-2530) | [$3](https://www.digikey.com/product-detail/en/adafruit-industries-llc/2530/1528-2777-ND/9770538) |
| IRLB8721 N-channel power MOSFET | 3 | [$1.75](https://www.adafruit.com/product/355) | [$0.85](https://www.mouser.com/ProductDetail/942-IRLB8721PBF) | [$0.85](https://www.digikey.com/product-detail/en/infineon-technologies/IRLB8721PBF/IRLB8721PBF-ND/2127670) |
| ULN2803A - 8-channel Darlington driver | 1 | [$2](https://www.adafruit.com/product/970) | [$1.20](https://www.mouser.com/ProductDetail/511-ULN2803A) | [$1.20](https://www.digikey.com/product-detail/en/stmicroelectronics/ULN2803A/497-2356-5-ND/599591) |
| 5W 5&ohm; potentiometer | 1 | n/a | [$5.25](https://www.mouser.com/ProductDetail/774-026TB32R5A0B1A1) | [$5.25](https://www.digikey.com/product-detail/en/cts-electrocomponents/026TB32R5A0B1A1/CT2150-ND/203771) |
| 2W 5&ohm; resistor | 1 | n/a | [$0.20](https://www.mouser.com/ProductDetail/282-5.1-RC) | [$0.40](https://www.digikey.com/product-detail/en/yageo/FMP200JR-52-5R1/5.1ZCT-ND/2059023) |
| 2W 10&ohm; resistor | 1 | n/a | [$0.25](https://www.mouser.com/ProductDetail/282-10-RC) | [$0.35](https://www.digikey.com/product-detail/en/yageo/FMP200JR-52-10R/10ZCT-ND/2058964) |
| 2W 15&ohm; resistor | 1 | n/a | [$0.25](https://www.mouser.com/ProductDetail/282-15-RC) | [$0.35](https://www.digikey.com/product-detail/en/yageo/FMP200JR-52-15R/15ZCT-ND/2058972) |

Note: Yageo is Digikey and Xicon is Mouser - see the [AdaWiki partfinder](https://www.ladyada.net/wiki/partfinder).

### Shared

A 5V 2A power adapter, similar to this $7.50 [one](https://www.adafruit.com/product/1995) from Adafruit or pretty much any power adapter sold for the Raspberry Pi, must be 2A or more.

| Description | Quantity | Adafruit | Mouser | Digikey |
|-------------|----------|----------|--------|---------|
| 2200mAh 18650 Li-ion battery | 1 | [$10](https://www.adafruit.com/product/1781) | [$10](https://www.mouser.com/ProductDetail/485-1781) | [$10](https://www.digikey.com/product-detail/en/adafruit-industries-llc/1781/1528-1836-ND/5054543) |
| HUZZAH32 - ESP32 Feather Board | 1 | [$20](https://www.adafruit.com/product/3405) | [$20](https://www.mouser.com/ProductDetail/485-3405) | [$20](https://www.digikey.com/product-detail/en/adafruit-industries-llc/3405/1528-2181-ND/7244967) |


Similar [2600mAh 18650 battery](https://www.aliexpress.com/item/32822109986.html) from Aliexpress for $3.75.

### ESP32 alternatives

* $11 - [Olimex ESP32-DevKit-LiPo](https://www.olimex.com/Products/IoT/ESP32/ESP32-DevKit-LiPo/open-source-hardware).
* $21 - [SparkFun Thing Plus - ESP32 WROOM](https://www.sparkfun.com/products/15663).

There are no end of no-name ESP32 modules available on sites like Banggood and Espressif themselves produce the nice cheap [ESP32-DevKitC-32D](https://www.mouser.com/ProductDetail/356-ESP32-DEVKITC32D) - however the ESP32-DevKitC-32D and most of these other boards don't have a LiPo JST connector (and the no-name boards generally come with no documentation).

Note: the 32 is the original Espressif module, the 32D is the newer module and the 32U is the 32D with a connector for an external antennae rather than a builtin PCB antennae.

**Question:** the HUZZAH32 - ESP32 Feather Board provides separate Vbat and Vusb pins so you can easily construct a circuit that works off Vbat only. However the Olimex ESP32-DevKit-LiPo tries to be pin compatible with the ESP32-DevKitC-32D (which only supports power via 5V USB) - so, _if I read the schematic correctly_, you've just got the 5V input voltage pin and you'll either see 5V or 3.7V on this pin depending on whether you're using USB power or battery power. TODO: ask Olimex if there's anyway to construct a circuit such that you only get power via this pin if battery power is being used?

### Lighthouse mark 2

| Description | Quantity | Adafruit | Mouser | Digikey |
|-------------|----------|----------|--------|---------|
| Adafruit Pixie | 1 | [$15](https://www.adafruit.com/product/2741) |  [$15](https://www.mouser.com/ProductDetail/485-2741) | [$15](https://www.digikey.com/product-detail/en/adafruit-industries-llc/2741/1528-1538-ND/5804104) |
| Plastic gearbox motor - 200RPM | 2 | [$3](https://www.adafruit.com/product/3777) | [$3](https://www.mouser.com/ProductDetail/485-3777) | [$3](https://www.digikey.com/product-detail/en/adafruit-industries-llc/3777/1528-2589-ND/8687221) |
| Eccentric motor hub | 1 | [$1](https://www.adafruit.com/product/3880) | [$1](https://www.mouser.com/ProductDetail/485-3880) | [$1](https://www.digikey.com/product-detail/en/adafruit-industries-llc/3880/1528-2699-ND/9603622) |
| motor pulley | 1 | [$0.75](https://www.adafruit.com/product/3789) | [$0.75](https://www.mouser.com/ProductDetail/485-3789) | [$0.75](https://www.digikey.com/product-detail/en/adafruit-industries-llc/3789/1528-2602-ND/9342241) |
| DRV8833 motor driver breakout | 1 | [$5](https://www.adafruit.com/product/3297) | [$5](https://www.mouser.com/ProductDetail/485-3297) | [$5](https://www.digikey.com/product-detail/en/adafruit-industries-llc/3297/1528-1795-ND/6419360) |
| JST on/off switch | 1 | [$3](https://www.adafruit.com/product/3064) | [$3](https://www.mouser.com/ProductDetail/485-3064) | [$3](https://www.digikey.com/product-detail/en/adafruit-industries-llc/3064/1528-1679-ND/6022074) |
| motor mounting bracket | 1 | [$1.50](https://www.adafruit.com/product/3768) | [$1.50](https://www.mouser.com/ProductDetail/485-3768) | [$1.50](https://www.digikey.com/product-detail/en/adafruit-industries-llc/3768/1528-2593-ND/9092301) |

Alternative motor and wheel (use the wheel as a hub to which to attach things):

* $3.50 - [DC motor in micro servo body](https://www.adafruit.com/product/2941)
* $2.50 - [matching wheel for motor](https://www.adafruit.com/product/2744)

### JST cable assemblies

| Description | Quantity | Adafruit | Mouser | Digikey |
|-------------|----------|----------|--------|---------|
| 2-pin cable set | 1 | [$1.50](https://www.adafruit.com/product/2880) | [$1.50](https://www.mouser.com/ProductDetail/485-2880) | [$1.50](https://www.digikey.com/product-detail/en/adafruit-industries-llc/2880/1528-1596-ND/5875804) |
| 3-pin cable set | 1 | [$1.50](https://www.adafruit.com/product/1663) | [$1.50](https://www.mouser.com/ProductDetail/485-1663) | [$1.50](https://www.digikey.com/product-detail/en/adafruit-industries-llc/1663/1528-2108-ND/7035021) |
| 4-pin cable set | 1 | [$1.50](https://www.adafruit.com/product/578) | [$1.50](https://www.mouser.com/ProductDetail/485-578) | [$1.50](https://www.digikey.com/product-detail/en/adafruit-industries-llc/578/1528-1518-ND/5774321) |
| 5-pin cable set | 1 | [$1.50](https://www.adafruit.com/product/1664) | [$1.50](https://www.mouser.com/ProductDetail/485-1664) | [$1.50](https://www.digikey.com/product-detail/en/adafruit-industries-llc/1664/1528-2109-ND/7035022) |
| 6-pin cable set | 1 | [$1.50](https://www.adafruit.com/product/1665) | [$1.50](https://www.mouser.com/ProductDetail/485-1665) | [$1.50](https://www.digikey.com/product-detail/en/adafruit-industries-llc/1665/1528-2110-ND/7035023) |

I already have the 4 pin one.

There must be similar sets from companies other than Adafruit on Mouser and Digikey but I can't find them.

Servo cable extension (cut in two and use as an cable set) - [$2](https://www.adafruit.com/product/972).

Sugru family-safe black 3 pack:

* [$10](https://sugru.com/buy/family-safe-skin-friendly-formula-black-3-pack) direct from Sugru.
* [Fr. 10](https://www.galaxus.ch/en/s4/product/sugru-klebeknete-family-safe-3-stueck-schwarz-glues-12446856) from Galaxus.

Note: Sugru has a relatively short shelf life of 13 months so be aware of this when buying.

High-viscosity glue from a glue gun might be just as good for this project.

### Heatsinks

* [7x7x10mm heatsink](https://www.adafruit.com/product/1493) - $1 - plus [thermal tape](https://www.adafruit.com/product/1467) - $1.
* [14x14x8 heatsink with thermal tape](https://www.adafruit.com/product/3083) - $1.50

### Heat shrink tubing

* [40mm 1m clear (also other sizes, lengths and colors)](https://www.ebay.ie/itm/PVC-Heat-Shrink-Tubing-Wrap-RC-Battery-Pack-17mm-150mm-LiPO-NiMH-NiCd-UK/361275492238?hash=item541db2878e:m:mNnqT-c4-rSEm2ffvforNOg&var=630618338782) - GBP 2 - eBay UK based.
* [40mm 1m clear (also other sizes, lengths and colors)](https://www.ebay.ie/itm/17MM-103MM-Wide-PVC-Heat-Shrink-Tubing-Wrap-Lipo-Li-ion-NiCd-RC-Battery-Pack/390950012124?hash=item5b066fc4dc:m:mVJw-PpLDMtp3s11XBOzDPw&var=660566819761) - GBP 1 - eBay China based.
* [40mm 2m clear (also other sizes, lengths and colors)](https://www.ebay.ie/itm/17mm-103mm-PVC-Heat-Shrink-Tubing-Wrap-Lipo-Li-ion-Ni-MH-NiCd-RC-Battery-Pack/252489229777?hash=item3ac987e5d1:m:mQX3I1eDATr8xytV50t9PnQ&var=551375439082) - GBP 1.60 - eBay China based.
