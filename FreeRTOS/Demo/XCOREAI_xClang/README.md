# XCORE.AI Demo for XCORE.AI Explorer Board
This page documents a FreeRTOS demo application that targets [XCORE.AI](https://www.xmos.ai/xcore-ai/).
The project uses [XMOS XTC Tools](https://www.xmos.ai/software-tools/) to build
the FreeRTOS XCOREAI port. This application demonstrates the [Symmetric
Multiprocessing (SMP)](https://github.com/FreeRTOS/FreeRTOS-Kernel/tree/smp) support in the FreeRTOS Kernel.

----

## IMPORTANT! Notes on using the FreeRTOS XCORE.AI demo application

Please read all the following points before using this RTOS port.

1. [Source Code Organization](#source-code-organization)
1. [The Demo Application](#the-demo-application)
1. [Building and Running the RTOS Demo Application](#building-and-running-the-rtos-demo-application)
1. [RTOS Configuration and Usage Details](#rtos-configuration-and-usage-details)

Also see the FAQ [My application does not run, what could be wrong](https://www.freertos.org/FAQHelp.html)?

----

## Source Code Organization
The project files for this demo are located in the `FreeRTOS/Demo/XCOREAI_xClang/RTOSDemo`
directory. FreeRTOS Port files compiled in the project are in the
`FreeRTOS/Source/portable/ThirdParty/xClang/XCOREAI` directory.

----

## The Demo Application
The constant `testingmainBLINKY_DEMO`, which is defined at the top of
`testing_main.h`, is used to switch between a simple "blinky" style getting
started project and a more comprehensive test and demo application.

### When testingmainBLINKY_DEMO is set to 1
When `testingmainBLINKY_DEMO` is set to 1, the demo application creates
two tasks which toggle 2 on-board LEDs (LED 0 and LED 1) periodically.

### When testingmainBLINKY_DEMO is set to 0
When `testingmainBLINKY_DEMO` is set to 0, the demo application creates standard
demo tasks. Standard demo tasks are used by all FreeRTOS port demo applications.
They have no specific functionality, and are created just to demonstrate how to
use the FreeRTOS API, and test the RTOS port.

Two "check" tasks are created that periodically inspect the standard demo tasks
(which contain self monitoring code) to ensure all the tasks are functioning as
expected. One check task monitors the demo tasks running on tile 0 and toggles
the LED 0 each time it executes. The other check task monitors the demo tasks
running on tile 1 and toggles the LED 1 each time it executes. This gives visual
feedback of the system health. **If both the LEDs toggle every 3 seconds, then the
check tasks have not discovered any problems. If any LED toggles every 200ms,
then the check task has discovered a problem in one or more tasks.**

----

## Building and Running the RTOS Demo Application

### Hardware setup
Plug the xTAG programmer into the evaluation board. Ensure both the xTAG and
evaluation board are connected to the computer via USB.

### Toolchain installation
1. Download the XMOS XTC Tools from [here](https://www.xmos.ai/software-tools/).
2. Uncompress the archive to your chosen installation directory. The example
below will install to your home directory:
```sh
$ tar -xf archive.tgz -C ~
```
3. Configure the default set of environment variables:
```sh
$ cd ~/XMOS/XTC/15.1.0
$ source SetEnv
```
4. Check that your tools environment has been setup correctly:
```sh
$ xcc --help
```
5. Make the XTAG drivers accessible to all users. This step is only required
once on a given development machine.
```sh
$ cd ~/XMOS/XTC/15.1.0/scripts
$ sudo ./setup_xmos_devices.sh
```
6. check that the XTAG devices are available and accessible:
```sh
$ cd ~/XMOS/XTC/15.1.0/scripts
$ ./check_xmos_devices.sh
Searching for xtag3 devices...
0 found
Searching for xtag4 devices...
1 found
Success: User <username> is able to access all xtag4 devices
```
7. Check that the device is available for debugging:
```sh
$ xrun -l
Available XMOS Devices
----------------------

  ID  Name            Adapter ID    Devices
  --  ----            ----------    -------
  0   XMOS XTAG-4     2W3T8RAG      P[0]
```

### Build and Run the demo application
1. Go to the RTOSDemo directory:
```sh
$ cd FreeRTOS/Demo/XCOREAI_xClang/RTOSDemo
```
2. Build the demo:
```sh
$ make
```
3. Run the demo:
```sh
$ make run
```
----

## RTOS Configuration and Usage Details

* Configuration items specific to this demo are contained in
`FreeRTOS/Demo/XCOREAI_xClang/RTOSDemo/src/FreeRTOSConfig.h`. The
[constants defined in that file](https://www.freertos.org/a00110.html) can be
edited to suit your application. The following configuration options are
specific to the SMP support in FreeRTOS Kernel:
  * `configNUM_CORES` - Set number of cores.
  * `configRUN_MULTIPLE_PRIORITIES` - Enable/Disable running multiple priorities tasks simultaneously.
  * `configUSE_CORE_AFFINITY` - Enable/Disable setting tasks affinity to cores.
* `Source/Portable/MemMang/heap_4.c` is included in the project to provide the
memory allocation required by the RTOS kernel. Please refer to the
[Memory Management](https://www.freertos.org/a00111.html) section of the API
documentation for full information.
* vPortEndScheduler() has not been implemented.