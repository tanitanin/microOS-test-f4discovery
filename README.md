# Blinky demo for STM32F4DISCOVERY, using µOS++

This demo program blinks the 4 coloured LEDs on the STM32F4DISCOVERY.

It demonstrates:

- how to organise a project using the content of several xPacks, including the **µOS++ IIIe / CMSIS++** code;
- how to integrate the CubeMX initialisation code.

This project **does not** demonstrate any µOS++ features, the only RTOS feature used is the system clock `sleep_for()`, to delay execution for a number of ticks.

## Preliminary

The content of the xPacks is not part of the repository, and must be dynamically generated. Until the XCDL utility will be available, the `generated` folder should be created with the `scripts/generate.sh` script.

```
bash scripts/generate.sh
```

Caution: Be sure subsequent runs of `generate.sh` are done when the Eclipse project is not opened.

## Prerequisites

### GCC 5.x

µOS++ IIIe uses several C++14 features, available from GCC 5.x up. To be able to build the project, be sure the `arm-none-eabi-gcc` from [Launchpad](http://launchpad.net/gcc-arm-embedded) is installed; for more details, please follow the [How to install the ARM toolchain?](http://gnuarmeclipse.github.io/toolchain/install/) instructions from the GNU ARM Eclipse web.

## Import the project in Eclipse

This project is an Eclipse managed project. To build it, import it to Eclipse:

- General → Existing Project into Workspace
- Select root directory
- be sure Copy projects into workspace is **disabled**
- select the `f4discovery-blinky-micro-os-plus` project
- import

Caution: Be sure the the project is imported **after** the system code was generated, and subsequent runs of `generate.sh` are done when the Eclipse project is not opened.

## Build

As for any Eclipse CDT managed project, Eclipse automatically creates the Makefiles needed to build the project. 

The procedure to perform the build is simple:

- Eclipse menu → Project → Build All

The result of this command are two folders, one Debug and one Release, each with all build related files, the most important ones being the ELF files.

## Build details

### Include folders

- `include`
- `cube-mx/Inc`
- `generated/arm-cmsis/core/include`
- `generated/micro-os-plus-iii/include`
- `generated/micro-os-plus-iii-cortexm/include`
- `generated/stm32f4-cmsis/stm32f407xx/include`
- `generated/stm32f4-hal/include`

### Source folders

- `cube-mx/Src`
- `generated/micro-os-plus-iii-cortexm/src`
- `generated/micro-os-plus-iii/src`
- `generated/stm32f4-cmsis/stm32f407xx/src`
- `generated/stm32f4-hal/src`
- `src`

### Compile options

The µOS++ code uses modern C++ features, and for this it is necessary to use a recent GCC version (v5.x or highrer) and to specify `-std=gnu++1y` in the GCC command line.

### Additional settings

- for `generated/stm32f4-hal/src`, to silence C warnings, use `-Wno-sign-conversion -Wno-padded -Wno-conversion -Wno-unused-parameter -Wno-bad-function-cast -Wno-sign-compare`


## Semihosting

To simplify testing, this demo uses the ARM semihosting API to access the host; this is enabled via `OS_USE_SEMIHOSTING_SYSCALLS` defined on the compiler command line. To run, semihosting normally requires an active debugger connection with the debugged board. However, the µOS++ exception handlers are enhanced with code to fake the semihosting calls if the debugger connection is not active (the JTAG/SWD cable is not connected). Unfortunately these features are available only for ARMv7M devices, so, for small Cortex-M0/M0+ devices, the debugger connection is manadatory.

For standalone applications, remove `OS_USE_SEMIHOSTING_SYSCALLS` and possibly add `-ffreestanding` to the compiler command line.
