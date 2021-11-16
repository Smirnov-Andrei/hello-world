<h1 align="center">ESP32 micropython & webREPL installation guide</h1>
<h2 align="center">

<p align="left">
1. [Introduction] [Introduction]
</p>

<p align="center">
[Introduction]: #Introduction
1. Micropython is written in C, the source is open source. Current version 1.17
2. The documentation is good https://docs.micropython.org/en/latest/index.html
3. MicroPython is implemented on top of ESP-IDF v.4, Espressif SDK for ESP32
 a. ESP-IDF - FreeRTOS based system
 b. Micropython runs in a RTOS task With 16 kb stack and 96 kb heap.
 c. Contains a file system based on FatFs.
4. The micropython interpreter (REPL) is loaded immediately upon booting the ESP MCU.
5. The standard execution of custom functions is supported by running python scripts (uploaded by users);
also scripts can be uploaded dynamically via REPL (read print cycle with evaluation).
6. Ways to use REPL (only one of them works at a time):
 a. by UART (115200 baud.
 b. WebRepl (start initial launch)
7.boot.py is always loaded first after loading the MP kernel, followed by
him main.py other custom scripts can be called from
first two
8. To use MP, you need to fill in the ESP32 bin-file.
There are ready-made images for some hardware solutions
(https://micropython.org/download/esp32/),
9. You can build it yourself from the source. More details in the file "How to build
Micropython.txt "
</p>


# ESP32 micropython & webREPL installation guide
## Introduction / Brief info
1. Micropython is written in C, the source is open source. Current version 1.17
2. The documentation is good https://docs.micropython.org/en/latest/index.html
3. MicroPython is implemented on top of ESP-IDF v.4, Espressif SDK for ESP32
 a. ESP-IDF - FreeRTOS based system
 b. Micropython runs in a RTOS task With 16 kb stack and 96 kb heap.
 c. Contains a file system based on FatFs.
4. The micropython interpreter (REPL) is loaded immediately upon booting the ESP MCU.
5. The standard execution of custom functions is supported by running python scripts (uploaded by users);
also scripts can be uploaded dynamically via REPL (read print cycle with evaluation).
6. Ways to use REPL (only one of them works at a time):
 a. by UART (115200 baud.
 b. WebRepl (start initial launch)
7.boot.py is always loaded first after loading the MP kernel, followed by
him main.py other custom scripts can be called from
first two
8. To use MP, you need to fill in the ESP32 bin-file.
There are ready-made images for some hardware solutions
(https://micropython.org/download/esp32/),
9. You can build it yourself from the source. More details in the file "How to build
Micropython.txt "

## Using micropython on esp32
1. Features of a micropython build for (support of different hardware features) are contained in the "machine" module. For ESP32 there are 2 more
additional modules "esp" and "esp32"
2.Using the os, sys, gc, micropython modules, you can get some
debug information, about all global variables, free space in
memory and flash.
3. In different micropython assemblies, the number and type of modules may differ; the actual
composition can be checked like this: 
```sh
>>> help ('modules')
```
4. More detailed information on each module can be obtained again through help
```sh
 >>> help (machine)
 >>> help (machine.SPI)
 ```
For the information to be displayed, the module must be connected via python command 
```py
import machine
```
Importing a module that is inside a folder for example
machine / SPI is done in the following way in order to save memory.
```py
from machine import SPI
```
5. There are three kinds of spi in Micropython -esp32 https://esp32.com/viewtopic.php?t=11469
 a. SoftSPI (programmable, for any legs),
 b. HSPI https://docs.micropython.org/en/latest/esp32/quickref.html#hardware-spibus
 c. VSPI (Physically started VSPI at half clock 80MHz, checked
oscillograph)
6. It should be borne in mind that the numbering of the GPIO pins is based on the numbering
ESP32 chips.
7. The firmware is uploaded using esptool via UART0.
```sh
esptool.py --chip esp32 --port / dev / ttyUSB0 erase_flash
esptool.py --chip esp32 --port / dev / ttyUSB0 --baud 460800 write_flash -z 0x1000 esp32-xxxxxxx-v1.10.bin
```
8. I used Thonny as IDE (alternative is PyCharm, or VS Code
(with the Pymakr))


## Using C / C ++ Code
1. You can add your own C code to Micropython. C ++ code is also possible, but for now
did not have time to achieve this, but in C everything worked out.
2. In this case, it is necessary to observe a certain style in order to be
the ability to call these functions from the MP. Starting from version 1.10 you can
add your (compiled mpy-cross) modules xxx.mpy directly to
file system MCU. The process of compiling an mpy file from C is described in
file "How to compile C to mpy.txt". Based
https://docs.micropython.org/en/latest/develop/cmodules.html
3. The resulting file is either copied to the file system, or attached to
the firmware image. If in the file system, then the bytecode (from mpy) will be in RAM
at run time. If it was included in the firmware image, then from the flash.
4. The second way is to compile with the core of micropython itself. More in
file "How to build Micropython.txt".
5. Also tried building a kernel with C ++ code from
~ / micropython / examples / usermodule / Build from this source
https://micropython-usermod.readthedocs.io/en/latest/usermods_05.html. Collected,
but after pouring into ESP32 I did not see these modules, maybe not everywhere
prescribed.
6. On the use of C ++, it is most fully described here:
https://github.com/stinos/micropython-wrap 
