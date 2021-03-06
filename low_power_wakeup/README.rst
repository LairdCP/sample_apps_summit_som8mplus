.. _low_power_wakeup:

Low Power Wakeup Example
##############################

Overview
********

This example demonstrates the low power capabilities of the Summit SOM 8M Plus
(and DVK). The A core can be put into the Deep Sleep Mode (DSM), with the DDR
turned off, and the M7 core can continue to run and wake the A core as needed.

NOTE: This example does not support running the M7 core from DDR or QSPI.

Building and Running
********************

This application can be built and executed on the Summit SOM 8M Plus
DVK as follows:

.. zephyr-app-commands::
   :zephyr-app: samples/low_power_wakeup
   :host-os: unix
   :board: summitsom8mplus_dvk_itcm
   :goals: run
   :compact:

Prepare the Demo
****************
1.  Copy the sample binary to the ``boot`` directory of the microSD card and
    insert it into the target board.
2.  Connect a USB cable between the host PC and the J2 USB port on the target
    board.
3.  Connect 12V power supply and switch S9 to power on the board.
4.  Open two serial terminals for A53 core and M7 core with the following
    settings:

    * 115200 baud rate
    * 8 data bits
    * No parity
    * One stop bit
    * No flow control
5.  From the U-Boot prompt, load the sample binary into the M7 core and boot it
    by following the steps in `Flashing and Booting the M7 Core`_.
6.  After booting the M7 core, using the ``boot`` command to boot the kernel on
    the A core terminal
7.  After the kernel has booted, login.

NOTES
1.  After the M7 core is running, please boot the Linux kernel. Make sure the
    FDT file is correctly set before booting the Linux kernel. The following
    commands can be used to set the FDT file in the U-Boot console:

    .. code-block:: console

        u-boot=>setenv fdtfile <target-fdtfile.dtb>
        u-boot=>saveenv

Running the Demo
****************
#. After the boot process succeeds, login to Linux with the standard username
   and password.

    * Username: root
    * Password: summit

#.  At the M7 core terminal, the shell prompt will appear:

    .. code-block:: console

        uart:~$

#.  At the Linux prompt, put the A core into the DSM state with the following
    command:

    .. code-block:: bash

        systemctl suspend

    The entire SOM is now in a low power state (A core and M7 core in DSM state).

#. At the M7 core's shell prompt, type ``wakeup`` to trigger the A core to exit
   the DSM state. The A core will wake up, and the Linux prompt will reappear.

    .. code-block:: console

        uart:~$ wakeup

Flashing and Booting the M7 Core
********************************
Below are the set of U-Boot commands to load and boot the M7 core. Currently,
one run-mode is supported: ITCM. These steps assume the name of the binary to be
``zephyr.bin``.

Flash and Boot M7 Core
=======================================
.. code-block:: console

    fatload mmc 1:1 0x48000000 zephyr.bin
    cp.b 0x48000000 0x7e0000 20000
    bootaux 0x7e0000
