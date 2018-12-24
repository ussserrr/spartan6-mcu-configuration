## Description
Proven example showing how to configure (flash the firmware) Xilinx Spartan-6 FPGA (XC6SLX-2FTG256C) using ARM MCU (Texas Instruments Tiva-C TM4C1294NCPDT). The design is not strictly hardware-specific and can be easily ported to another MCU and similar FPGA.

Chosen method - slave serial. It is a basic synchronous, single data-wire interface. The bitstream is stored in MCU Flash memory.


## Bitstream
You need to generate the `.bin` bitstream using Xilinx BitGen utility with following command-line options:

```
-g Binary:yes
```

In PlanAhead, for example, it can be done via GUI in the 'Bitstream Settings' section of Flow Navigator:
![planahead](/planahead.png)

The bitstream is stored in MCU Flash memory in form of pair of `.c/.h` files.

 - bitstream.c:

```C
#include <bitstream.h>

const unsigned char bitstream_fw[] = {
    ...
    0x00, 0x93, 0x30, 0xe1, 0xff, 0xcf, 0x30, 0xc1, 0x00, 0x81, 0x31, 0x81,
    ...
};
```

 - bitstream.h

```C
#ifndef BITSTREAM_H_
#define BITSTREAM_H_

extern const unsigned char bitstream_fw[];

#endif /* BITSTREAM_H_ */
```

 - main.c

```C
...
#include "bitstream.h"
#define SPARTAN_FW_SIZE 54664  // in bytes
...
```

It always has the same size no matter what functionality your firmware is providing. To convert `.bin` file to C header use some sort of converter program (e.g. using Python or C on your PC).


## Pinout
General schematic (omit the JTAG part) (see UG380):
![pinout](/pinout.png)

Note that some "feedback" pins such as INIT_B can be not routed and their functionality can be replaced by a simple delay.


## References
 - [UG380](https://www.xilinx.com/support/documentation/user_guides/ug380.pdf)
 - [XAPP502](https://www.xilinx.com/support/documentation/application_notes/xapp502.pdf)
 - [UG628](https://www.xilinx.com/support/documentation/sw_manuals/xilinx14_5/devref.pdf)
