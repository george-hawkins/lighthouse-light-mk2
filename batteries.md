TLDR; if you want to draw more current, than supplied by the hobbyist li-ion cells from Adafruit and Sparkfun, then use a branded 18650 cell with a high C reading and built-in protection circuit along with a battery holder designed to accommodate the non-standard height of such cells.

---

Li-ion batteries are a borderline hobbyist component. Many electronics projects include a jokey disclaimer to the effect that the thing in question _may_ burn your house down. Li-ion batteries, if not handled correctly, definitely will burn your house down.

Li-ion batteries are inconventient to handle and inconventient to dispose of responsibly - but they can provide more power classic NiMH rechargeables (and don't suffer from the [memory effect](https://en.wikipedia.org/wiki/Memory_effect)).

However they're quite different from classic non-rechargeable alkaline batteries and NiMH rechargeables. These classic batteries don't need any supporting electronics and can be mishandled without any real dire consequences. Li-ion batteries in contrast will go on fire if mishandled and require a protection circuit to function safely.

The protection circuit typically do several things - it provides overcharging, overheating, shorting and undervoltage protection.

Overcharing, overheating and shorting will cause the battery to puff up (due to the build up of gasses within the cell) and eventually to catch fire.

Undervoltage in contrast is protecting the battery from you - with classic NiMH rechargeables you can just use them until they stop working and then recharge them. Li-ion batteries have a _nominal_ voltage of 3.7V, however when fully charged they provide about 4.2V - the voltage drops as the battery discharges. Eventually it'll reach 3V - if the voltage is allowed to drop below this point the battery will reach a state from which it cannot be charged.

The least consumer safe li-ion batteries are those used by [RC](https://en.wikipedia.org/wiki/Radio-controlled_model) hobbyists. These come wihout protection circuit or any kind of protective casing. It's up to separate circuitry in the RC vehicle to monitor the state of the battery.

In contrast the li-ion cells sold by hobbyist sites like Adafruit and Sparkfun come with a protection circuit as part of the battery. But with the exception of 18650 based cells (which we'll come to), they don't have any protective casing.

Finally the most consumer safe li-ion batteries are the powerbanks that you find everywhere for providing reserve power for your mobile phone and other portable consumer electronic devices. The li-ion cells are protected by a hard plastic case and the electronics provide the fixed 5V, that USB devices require, and limit the current.

These batteries may be safe and convenient but they're the least flexible for hobbyist projects. The fixed voltage, limited current and non-standard sizes don't make them great to work with (though you do find them being used in cheap robot projects. This [JetBot kit](https://www.sparkfun.com/products/15365) from Sparkfun uses one - but it also demonstrates the limitations of such powerbanks as the Nvidia Nano used in the project has to be run in a low-power mode due to the limited current, in contrast to this [JetBot kit](https://www.waveshare.com/jetbot-ai-kit-acce.htm) from Waveshare that uses 18650 batteries and can run the Nano at full power.

Most of the li-ion batteries provides by hobbyist sites come in completely non-standard shapes - the exception are those based on what are called [18650 cells](https://en.wikipedia.org/wiki/List_of_battery_sizes#Cylindrical_lithium-ion_rechargeable_battery).

If you crack open (not recommended) a powerbank you'll find it full of 18650 cells and if you crack open a Tesla battery pack you'll find more the 7 thousand of them.

They come in a standard form factor similar but larger than a classic AA battery. While they have a standard shape there are no end of variations with different [C ratings](https://oscarliang.com/lipo-battery-c-rating/) and different [mAh capacities](https://oscarliang.com/lipo-battery-guide/#capacity).

These cells aren't typically sold directly to consumers and, like all li-ion batteries, their tendency to catch fire is mishandled makes them very unpopular with postal services, e.g. the UK postal service will destroy your whole package if they find it contains lose li-ion cells, i.e. cells that are not built into or attached to an electronic device).

This makes them quite hard to buy. Most sites don't want to deal with the extra postage issues involves and even big electronic component sites like Mouser or Digikey, that carry every conceivable variant of everything else, don't really stock such batteries (they do cell a few as they act as distributors for the cells sold by Adafruit and Sparkfun and a few other hobbyist sites).

---

You can easily stick a thumb-tack into such a cell, shorting the internal layers and causing it to catch fire.

So you end up having to buy them from specialist sites.

They're heavily counterfieted, i.e. cheap no-name Chinese cells are repackaged to look like branded cells from Samsung etc. (whose cells look very generic anyway).

Manufacturers generally bundle unprotected cells together into packs and then provide protection and recharging circuitry for the full pack.

When using boards like the Adafruit [Feathers](xxx) the recharging circuitry is provided by the board but you still need a protected cell.

So while 18650 cells are generally sold unprotected, you can buy them with a tiny [disc shaped protection circuit](https://www.banggood.com/1S-3_7V-18650-Lithium-Battery-Protection-Board-2_5A-Li-ion-BMS-with-Overcharge-and-Over-Discharge-Protection-p-1529056.html) attached to the base.

---

Spot welding - DIY

Battery holder - protected / unprotected.

Samsung Note, no-name hoverboards.

While many people do successfully build their own battery packs without apparent issue, the consensus seems to be that applying extreme heat, even momentarily, to something as already temperamental as a li-ion battery isn't recommended.

Buying the protection board separately gives you more flexibility - you can use any unprotected cell - but you have to make sure to match the maximum discharge current of the board to the the maximum discharge current supported by the cell.

---

Knockoffs of xxx battery holder for *protected* 18650, [with leads](https://www.banggood.com/DIY-1-Slot-18650-Battery-Holder-With-2-Leads-p-972775.html) and [without leads](https://www.banggood.com/DIY-Storage-Box-Holder-Case-For-1-x-18650-Rechargeable-Battery-p-1006063.html).

[Battery holder](https://www.banggood.com/1-Slots-18650-Battery-Box-Rechargeable-Battery-Holder-Board-for-1x18650-Batteries-DIY-kit-Case-p-1472104.html) for *unprotected* 18650.

Aliexpress TZT 1S boards:

* [3A](https://www.aliexpress.com/item/32803639402.html)
* [5A](https://www.aliexpress.com/item/32809288456.html
* [7.5A, 10A and 15A](https://www.aliexpress.com/item/32956094047.html) - select 3MOS for 7.5A, 4MOS for 10A and 6MOS for 15A.

Banggood 1S boards:

* [2A](https://www.banggood.com/1S-3_7V-2A-li-ion-BMS-PCM-18650-Battery-Protection-Board-PCB-for-18650-Lithium-ion-li-Battery-p-1538099.html)
* [4A](https://www.banggood.com/1S-3_7V-4A-li-ion-BMS-PCM-18650-Battery-Protection-Board-PCB-for-18650-lithium-Battery-Double-MOS-p-1538089.html)

eBay:

* [1A to 12A](https://www.ebay.co.uk/itm/1S-Cell-PCB-BMS-18650-Protection-Board-Li-ion-Lithium-Battery/173955620419) - ships from UK.
* [5A, 10A and 15A](https://www.ebay.co.uk/itm/5-10-15A-1S-3-7V-18650-MOS-Li-ion-Lithium-Battery-Charging-BMS-Protection-Board/283300258134) - ships from China.
