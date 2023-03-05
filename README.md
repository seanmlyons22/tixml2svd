# tixml2svd

This utility creates
[SVD](https://www.keil.com/pack/doc/CMSIS/SVD/html/svd_Format_pg.html)
files from the Texas-Instruments XML (called TIXML from now on) device
and peripheral descriptor files.

Device descriptor files are generally found in the
ccsxxxx/ccs/ccs_base/common/targetdb/devices directory of a TI Code Composer
installation directory. They contain the names and base addresses of
all of the device's peripherals, as well as a the relative path of
each peripheral's descriptor file.

Peripheral descriptor files are generally found in the
ccsxxxx/ccs/ccs_base/common/targetdb/Modules directory of a TI Code Composer
installation directory. They contain the names and addresses of all of
the registers belonging to a peripheral.

## Usage

Here is an one way to copy the device and Module directories into a
local work directory and convert the line endings of all files to unix
platform line endings:

```
mkdir tmp
cp -r /ext/ti/ccs1220/ccs/ccs_base/common/targetdb/devices tmp
cp -r /ext/ti/ccs1220/ccs/ccs_base/common/targetdb/Modules tmp
find tmp -type f -exec dos2unix \{\} \;
```

Now, process a device file with something like `tixml2svd -z -i
devices/cc2652r1f.xml > cc2652r1f.svd`. If this does not work, try one
of the device peripherals all by itself, with something like
`tixml2svd -p -i Modules/CC26xx/CC26x0R2F/IOC.xml > IOC.svd`.

## Caveats

I have tested this code on almost all Code Composer version 8 MSP430,
MSP432, and CC* wireless devices. When provided with the -z option, it
generates a sanitized SVD file that can be used by
[svd2rust](https://crates.io/crates/svd2rust) to generate peripheral
access code.

Some of the CCSv8 device files contain errors, ununique peripheral,
register or field names, or ununique enumeration values. With the
sanitize option, tixml2svd deals with most of these problems
automatically, but there are exceptions. The following
[patch](./doc/ccsv8_dev.diff) file makes it possible to apply
tixml2svd to all 644 tixml device files on my machine (see below), and
successfully generate rust code if rust warnings are permitted by your
svd2rust library. Many rust code libraries generated by tixml2svd and
svd2rust compile without warnings, but not all of them. The tixml
files for some devices may contain mistakes that the compiler is
warning you about.

Note that some TI register fields contain enumerations
that do completely fit inside their field. I believe that this is
done to permit these enumerations to apply to multiple fields, but
these enumerations will cause svd2rust to exit with an error.

For the moment, tixml2svd does not generate complete SVD device headers. By
default, it adds a fake device header that you must modify by hand.
This will require a little research on your part. Here is an example
of the information you will need to dig up (the following header
provides sufficient information for the Segger Ozone debugger).

```
<?xml version="1.0" encoding="UTF-8"?>
<device xmlns:xs="http://www.w3.org/2001/XMLSchema-instance" schemaVersion="1.1" xs:noNamespaceSchemaLocation="CMSIS-SVD_Schema_1_0.xsd">
  <name>CC26x0</name>
  <version>2.3</version>
  <description>SimpleLink CC26xx Ultra-low power wireless MCU</description>
  <cpu>
    <name>CM3</name>
    <revision>r2p1</revision>
    <endian>little</endian>
    <mpuPresent>false</mpuPresent>
    <fpuPresent>false</fpuPresent>
    <nvicPrioBits>3</nvicPrioBits>
    <vendorSystickConfig>false</vendorSystickConfig>
  </cpu>
  <addressUnitBits>8</addressUnitBits>
  <width>32</width>
  <size>32</size>
  <access>read-write</access>
  <resetMask>0xFFFFFFFF</resetMask>
```

## Credits

- [nholstein](https://github.com/nholstein): Auto BOM detection, CPU header creation

## Devices

This is an incomplete list of devices that, after
[patching](./doc/ccsv8_dev.diff), can be converted to SVD:

cc1310f128,
cc1310f32,
cc1310f64,
cc1312r1f3,
cc1350f128,
cc1352p1f3,
cc1352r1f3,
cc2538nf11,
cc2538nf23,
cc2538nf53,
cc2538sf23,
cc2538sf53,
cc2620f128,
cc2630f128,
cc2640f128,
cc2640r2f,
cc2642r1f,
cc2650f128,
cc2652r1f,
CC430F5123,
CC430F5125,
CC430F5133,
CC430F5135,
CC430F5137,
CC430F5143,
CC430F5145,
CC430F5147,
CC430F6125,
CC430F6126,
CC430F6127,
CC430F6135,
CC430F6137,
CC430F6143,
CC430F6145,
CC430F6147,
cm2538sf23,
cm2538sf53,
MSP430AFE221,
MSP430AFE222,
MSP430AFE223,
MSP430AFE231,
MSP430AFE232,
MSP430AFE233,
MSP430AFE251,
MSP430AFE252,
MSP430AFE253,
MSP430BT5190,
MSP430C091,
MSP430C092,
MSP430C1111,
MSP430C111,
MSP430C1121,
MSP430C112,
MSP430C1331,
MSP430C1351,
MSP430C311S,
MSP430C312,
MSP430C313,
MSP430C314,
MSP430C315,
MSP430C323,
MSP430C325,
MSP430C336,
MSP430C337,
MSP430C412,
MSP430C413,
MSP430CG4616,
MSP430CG4617,
MSP430CG4618,
MSP430CG4619,
MSP430E112,
MSP430E313,
MSP430E315,
MSP430E325,
MSP430E337,
MSP430F1101A,
MSP430F1101,
MSP430F110,
MSP430F1111A,
MSP430F1111,
MSP430F1121A,
MSP430F1121,
MSP430F1122,
MSP430F112,
MSP430F1132,
MSP430F1222,
MSP430F122,
MSP430F1232,
MSP430F123,
MSP430F133,
MSP430F135,
MSP430F1471,
MSP430F147,
MSP430F1481,
MSP430F148,
MSP430F1491,
MSP430F149,
MSP430F155,
MSP430F156,
MSP430F157,
MSP430F1610,
MSP430F1611,
MSP430F1612,
MSP430F167,
MSP430F168,
MSP430F169,
MSP430F2001,
MSP430F2002,
MSP430F2003,
MSP430F2011,
MSP430F2012,
MSP430F2013,
MSP430F2101,
MSP430F2111,
MSP430F2112,
MSP430F2121,
MSP430F2122,
MSP430F2131,
MSP430F2132,
MSP430F2232,
MSP430F2234,
MSP430F2252,
MSP430F2254,
MSP430F2272,
MSP430F2274,
MSP430F2330,
MSP430F233,
MSP430F2350,
MSP430F235,
MSP430F2370,
MSP430F2410,
MSP430F2416,
MSP430F2417,
MSP430F2418,
MSP430F2419,
MSP430F2471,
MSP430F247,
MSP430F2481,
MSP430F248,
MSP430F2491,
MSP430F249,
MSP430F2616,
MSP430F2617,
MSP430F2618,
MSP430F2619,
MSP430F412,
MSP430F4132,
MSP430F413,
MSP430F4152,
MSP430F415,
MSP430F417,
MSP430F423A,
MSP430F423,
MSP430F4250,
MSP430F425A,
MSP430F425,
MSP430F4260,
MSP430F4270,
MSP430F427A,
MSP430F427,
MSP430F4351,
MSP430F435,
MSP430F4361,
MSP430F436,
MSP430F4371,
MSP430F437,
MSP430F438,
MSP430F439,
MSP430F447,
MSP430F4481,
MSP430F448,
MSP430F4491,
MSP430F449,
MSP430F46161,
MSP430F4616,
MSP430F46171,
MSP430F4617,
MSP430F46181,
MSP430F4618,
MSP430F46191,
MSP430F4619,
MSP430F47126,
MSP430F47127,
MSP430F47163,
MSP430F47166,
MSP430F47167,
MSP430F47173,
MSP430F47176,
MSP430F47177,
MSP430F47183,
MSP430F47186,
MSP430F47187,
MSP430F47193,
MSP430F47196,
MSP430F47197,
MSP430F477,
MSP430F4783,
MSP430F4784,
MSP430F478,
MSP430F4793,
MSP430F4794,
MSP430F479,
MSP430F5131,
MSP430F5132,
MSP430F5151,
MSP430F5152,
MSP430F5171,
MSP430F5172,
MSP430F5212,
MSP430F5213,
MSP430F5214,
MSP430F5217,
MSP430F5218,
MSP430F5219,
MSP430F5222,
MSP430F5223,
MSP430F5224,
MSP430F5227,
MSP430F5228,
MSP430F5229,
MSP430F5232,
MSP430F5234,
MSP430F5237,
MSP430F5239,
MSP430F5242,
MSP430F5244,
MSP430F5247,
MSP430F5249,
MSP430F5252,
MSP430F5253,
MSP430F5254,
MSP430F5255,
MSP430F5256,
MSP430F5257,
MSP430F5258,
MSP430F5259,
MSP430F5304,
MSP430F5308,
MSP430F5309,
MSP430F5310,
MSP430F5324,
MSP430F5325,
MSP430F5326,
MSP430F5327,
MSP430F5328,
MSP430F5329,
MSP430F5333,
MSP430F5335,
MSP430F5336,
MSP430F5338,
MSP430F5340,
MSP430F5341,
MSP430F5342,
MSP430F5358,
MSP430F5359,
MSP430F5418A,
MSP430F5418,
MSP430F5419A,
MSP430F5419,
MSP430F5435A,
MSP430F5435,
MSP430F5436A,
MSP430F5436,
MSP430F5437A,
MSP430F5437,
MSP430F5438A,
MSP430F5438,
MSP430F5500,
MSP430F5501,
MSP430F5502,
MSP430F5503,
MSP430F5504,
MSP430F5505,
MSP430F5506,
MSP430F5507,
MSP430F5508,
MSP430F5509,
MSP430F5510,
MSP430F5513,
MSP430F5514,
MSP430F5515,
MSP430F5517,
MSP430F5519,
MSP430F5521,
MSP430F5522,
MSP430F5524,
MSP430F5525,
MSP430F5526,
MSP430F5527,
MSP430F5528,
MSP430F5529,
MSP430F5630,
MSP430F5631,
MSP430F5632,
MSP430F5633,
MSP430F5634,
MSP430F5635,
MSP430F5636,
MSP430F5637,
MSP430F5638,
MSP430F5658,
MSP430F5659,
MSP430F6433,
MSP430F6435,
MSP430F6436,
MSP430F6438,
MSP430F6458,
MSP430F6459,
MSP430F6630,
MSP430F6631,
MSP430F6632,
MSP430F6633,
MSP430F6634,
MSP430F6635,
MSP430F6636,
MSP430F6637,
MSP430F6638,
MSP430F6658,
MSP430F6659,
MSP430F6720A,
MSP430F6720,
MSP430F6721A,
MSP430F6721,
MSP430F6723A,
MSP430F6723,
MSP430F6724A,
MSP430F6724,
MSP430F6725A,
MSP430F6725,
MSP430F6726A,
MSP430F6726,
MSP430F6730A,
MSP430F6730,
MSP430F6731A,
MSP430F6731,
MSP430F6733A,
MSP430F6733,
MSP430F6734A,
MSP430F6734,
MSP430F6735A,
MSP430F6735,
MSP430F6736A,
MSP430F6736,
MSP430F67451A,
MSP430F67451,
MSP430F6745A,
MSP430F6745,
MSP430F67461A,
MSP430F67461,
MSP430F6746A,
MSP430F6746,
MSP430F67471A,
MSP430F67471,
MSP430F6747A,
MSP430F6747,
MSP430F67481A,
MSP430F67481,
MSP430F6748A,
MSP430F6748,
MSP430F67491A,
MSP430F67491,
MSP430F6749A,
MSP430F6749,
MSP430F67621A,
MSP430F67621,
MSP430F67641A,
MSP430F67641,
MSP430F67651A,
MSP430F67651,
MSP430F6765A,
MSP430F6765,
MSP430F67661A,
MSP430F67661,
MSP430F6766A,
MSP430F6766,
MSP430F67671A,
MSP430F67671,
MSP430F6767A,
MSP430F6767,
MSP430F67681A,
MSP430F67681,
MSP430F6768A,
MSP430F6768,
MSP430F67691A,
MSP430F67691,
MSP430F6769A,
MSP430F6769,
MSP430F67751A,
MSP430F67751,
MSP430F6775A,
MSP430F6775,
MSP430F67761A,
MSP430F67761,
MSP430F6776A,
MSP430F6776,
MSP430F67771A,
MSP430F67771,
MSP430F6777A,
MSP430F6777,
MSP430F67781A,
MSP430F67781,
MSP430F6778A,
MSP430F6778,
MSP430F67791A,
MSP430F67791,
MSP430F6779A,
MSP430F6779,
MSP430FE4232,
MSP430FE423A,
MSP430FE423,
MSP430FE4242,
MSP430FE4252,
MSP430FE425A,
MSP430FE425,
MSP430FE4272,
MSP430FE427A,
MSP430FE427,
MSP430FG4250,
MSP430FG4260,
MSP430FG4270,
MSP430FG437,
MSP430FG438,
MSP430FG439,
MSP430FG4616,
MSP430FG4617,
MSP430FG4618,
MSP430FG4619,
MSP430FG477,
MSP430FG478,
MSP430FG479,
MSP430FG6425,
MSP430FG6426,
MSP430FG6625,
MSP430FG6626,
MSP430FR2000,
MSP430FR2032,
MSP430FR2033,
MSP430FR2100,
MSP430FR2110,
MSP430FR2111,
msp430fr2310,
msp430fr2311,
MSP430FR2422,
MSP430FR2433,
MSP430FR2512,
MSP430FR2522,
MSP430FR2532,
MSP430FR2533,
MSP430FR2632,
MSP430FR2633,
MSP430FR4131,
MSP430FR4132,
MSP430FR4133,
MSP430FR5041,
MSP430FR50431,
MSP430FR5043,
MSP430FR5720,
MSP430FR5721,
MSP430FR5722,
MSP430FR5723,
MSP430FR5724,
MSP430FR5725,
MSP430FR5726,
MSP430FR5727,
MSP430FR5728,
MSP430FR5729,
MSP430FR5730,
MSP430FR5731,
MSP430FR5732,
MSP430FR5733,
MSP430FR5734,
MSP430FR5735,
MSP430FR5736,
MSP430FR5737,
MSP430FR5738,
MSP430FR5739,
MSP430FR58471,
MSP430FR5847,
MSP430FR5848,
MSP430FR5849,
MSP430FR5857,
MSP430FR5858,
MSP430FR5859,
MSP430FR58671,
MSP430FR5867,
MSP430FR5868,
MSP430FR5869,
MSP430FR5870,
MSP430FR58721,
MSP430FR5872,
MSP430FR5887,
MSP430FR5888,
MSP430FR58891,
MSP430FR5889,
MSP430FR59221,
MSP430FR5922,
MSP430FR59471,
MSP430FR5947,
MSP430FR5948,
MSP430FR5949,
MSP430FR5957,
MSP430FR5958,
MSP430FR5959,
msp430fr5962,
msp430fr5964,
MSP430FR5967,
MSP430FR5968,
MSP430FR59691,
MSP430FR5969,
MSP430FR5970,
MSP430FR59721,
MSP430FR5972,
MSP430FR5986,
MSP430FR5987,
MSP430FR5988,
MSP430FR59891,
MSP430FR5989,
msp430fr5992,
MSP430FR59941,
msp430fr5994,
MSP430FR6035,
MSP430FR60371,
MSP430FR6037,
MSP430FR6041,
MSP430FR60431,
MSP430FR6043,
MSP430FR6045,
MSP430FR60471,
MSP430FR6047,
MSP430FR6820,
MSP430FR68221,
MSP430FR6822,
MSP430FR6870,
MSP430FR68721,
MSP430FR6872,
MSP430FR6877,
MSP430FR68791,
MSP430FR6879,
MSP430FR6887,
MSP430FR6888,
MSP430FR68891,
MSP430FR6889,
MSP430FR6920,
MSP430FR69221,
MSP430FR6922,
MSP430FR69271,
MSP430FR6927,
MSP430FR6928,
MSP430FR6970,
MSP430FR69721,
MSP430FR6972,
MSP430FR6977,
MSP430FR69791,
MSP430FR6979,
MSP430FR6987,
MSP430FR6988,
MSP430FR69891,
MSP430FR6989,
MSP430FW423,
MSP430FW425,
MSP430FW427,
MSP430FW428,
MSP430FW429,
MSP430G2001,
MSP430G2101,
MSP430G2102,
MSP430G2111,
MSP430G2112,
MSP430G2113,
MSP430G2121,
MSP430G2131,
MSP430G2132,
MSP430G2152,
MSP430G2153,
MSP430G2201,
MSP430G2202,
MSP430G2203,
MSP430G2210,
MSP430G2211,
MSP430G2212,
MSP430G2213,
MSP430G2221,
MSP430G2230,
MSP430G2231,
MSP430G2232,
MSP430G2233,
MSP430G2252,
MSP430G2253,
MSP430G2302,
MSP430G2303,
MSP430G2312,
MSP430G2313,
MSP430G2332,
MSP430G2333,
MSP430G2352,
MSP430G2353,
MSP430G2402,
MSP430G2403,
MSP430G2412,
MSP430G2413,
MSP430G2432,
MSP430G2433,
MSP430G2444,
MSP430G2452,
MSP430G2453,
MSP430G2513,
MSP430G2533,
MSP430G2544,
MSP430G2553,
MSP430G2744,
MSP430G2755,
MSP430G2855,
MSP430G2955,
MSP430i2020,
MSP430i2021,
MSP430i2030,
MSP430i2031,
MSP430i2040,
MSP430i2041,
MSP430L092,
MSP430P112,
MSP430P313,
MSP430P315S,
MSP430P315,
MSP430P325,
MSP430P337,
MSP430SL5438A,
MSP430TCH5E,
msp432e401y,
msp432e411y,
msp432p4011,
msp432p401m,
msp432p401r,
msp432p401v,
msp432p401y,
msp432p4111,
msp432p411v,
msp432p411y,
RF430F5144,
RF430F5155,
RF430F5175,
RF430FRL152H,
RF430FRL153H,
RF430FRL154H
