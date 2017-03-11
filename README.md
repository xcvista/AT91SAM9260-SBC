# AT91SAM9260-SBC

A Linux single board computer without BGA

## Introduction

Modern electronics runs on BGA packages. At least a lot of them do. Those dense
packages are good for making products more light and small, they are nightmares
to DIY makers and hand repairs.

This project is a design challenge: to build a single-board computer, not too
dissimilar from the infamous Raspberry Pi, that runs Linux, but don't use those
BGA chips. For some added difficulty, in this Round One design, no chip with a
pad on the bottom is allowed too. (or I would have cheated and spun for an V3s
that is pretty much an all-in-one solution.)

## Component Selection

### Processor

There are only four ARM processors that comes in in QFP package and runs Linux:

* Allwinner A13 (aka R8, as used in [C.H.I.P](https://getchip.com/))
* Allwinner V3s
* NXP i.MX233
* Atmel AT91SAM9260

Allwinner A13 requires the use of DDR3 memory. Sadly its memory controller does
not support the fly-by topology used on SODIMM modules, making soldering DDR3
memory chips, which only comes in BGA package, a must. This rules out the A13.

V3s is interesting as it comes with built-in RAM - it can work without any high
speed signals outside of the chip. However it requires the use of a QFN PMIC.
It will qualify for the Round 2, but not now.

i.MX233 have one special requirement: a special "Serial JTAG" adapter is needed
to program it for the first time. The required adapter is not open source, and
costs an arm and a leg to buy. This makes i.MX233 too complicated to use to me.

This leaves AT91SAM9260 as my option. This design takes the chip to its maximum
potential, to make its life running Linux a bit easier. The latest Linux kernel
is demanding.

### RAM, Flash and Boot arrangement

AT91SAM9260 uses SDR SDRAM. Those are a bit hard to find now. It took me a long
scout to find the chip that works: MT48LC32M16A2-75C. It is a PC-133 CL3 memory
chip that maxes out the memory controller on AT91SAM9260.

AT91SAM9260 supports only SLC NAND memory with one CS line. It appeared to me
that 512MB is the maximum capacity a 1CS NAND chip can go up to from Samsung,
one of the supported NAND supplier of AT91SAM9260. The processor can boot off
this NAND, but it may be a bit tight down the road. Thus some Plan B is added
to allow this board to boot off even an not-so-supported Intel SLC NAND chip.

The Plan B in question is a DataFlash chip on the SPI bus. This allows the chip
tp boot off the u-boot located in the NOR serial Flash, load the driver for
whatever NAND flash Iam currently using on the board, and load the kernel and
root filesystem off there. The DataFlash can even be expanded to hold the Linux
kernel and OpenFirmware device tree on itself.

### Other peripherals

AT91SAM9260 have an built-in Ethernet MAC. The Realtek RTL8201 Ethernet PHY is
used to interface the computer to the world. An 25AA02E48 is used to provide a
guaranteed unique MAC address for the board.

The onboard MMC controller goes to a microSD card slot for removeable storage
(permanent? There is only 512MB onboard.)

## License

This is [open source hardware](http://www.oshwa.org/), licensed under the
[3-clause BSD license](LICENSE.md).