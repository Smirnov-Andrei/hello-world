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

## Some features and conclusions
1. ESP-IDF is changing rapidly and MicroPython only supports certain
version. MicroPython currently supports v4.0.2, v4.1.1 and v4.2.
although other versions of IDF v4 may work as well. Therefore, the risks are for everyone
collected with new -specific versions of IDF.
2. There is a package manager upip. Puts packages directly to the hardware via WiFi (after
presetting)
3. It is better to import only the necessary, and not the entire module, for example.
```sh
>>> from upip import install
>>> upip.install ('picoweb')
```
4. The following pins are available on the ESP32 (inclusive): 0-19, 21-23, 25-27,
32-39. These numbers correspond to the physical GPIO pin numbers of the chip.
ESP32. It should be borne in mind that many boards use their own
pin numbering (e.g. D0, D1, etc.) must be matched.
5. Pins 1 and 3 are, respectively, TX- and RX-pins of the UART port for REPL
6. Pins 6, 7, 8, 11, 16 and 17 are used to connect to the built-in flash memory, so it is not recommended to use them for other purposes.
7. Acceleration of the script can be achieved not only by using C, but
also using @ micropython.native (which makes it possible
converting python code to native machine code.) and
@ micropython.viper (accessing registers directly
microprocessor). More details https://habr.com/ru/post/448702/
8. During assembly, one caveat emerged:
 On September 30, 2021, one certificate in the cacrt_all.pem file expired.
 Therefore, you have to disable the certificate in the file:
/ micropython / ports / esp32 / build-GENERIC / sdkconfig:
```sh
CONFIG_MBEDTLS_CERTIFICATE_BUNDLE = n (instead of y)
CONFIG_MBEDTLS_CERTIFICATE_BUNDLE_DEFAULT_FULL = n (instead of y)
 ```
You need to monitor esp-idf to enable it when renewing the certificate, otherwise
there is no possibility to work via https. Issue # 7660 has already been posted in esp-idf.
9. Great thing for rapid prototyping.


## How to build micropython
*tested in ubuntu 20.04 image as virtual machine*

```sh
	a. $sudo apt-get install git wget flex bison gperf python3 python3-pip python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util
	b. $sudo apt-get install python-is-python3 python3-pip python3-setuptools
	c. $pip3 install pyelftools>=0.25
```

2.install ESP32 toolchain 
```sh
	a. $mkdir esp
	b. $cd esp
	c. $git clone -b v4.2 --recursive https://github.com/espressif/esp-idf.git
	d. $cd esp-idf
	e. $./install.sh
	f. $nano ~/.bash_aliases
	g. add
		alias esp32_idf='. $HOME/esp/esp-idf/export.sh'

	   save&exit
```

3. Download micropython resp and compile mpy-cross compiler
```sh
	a.$cd $HOME
	b.$git clone https://github.com/micropython/micropython.git
	c.$cd micropython/mpy-cross
	d.$make
	e.$nano ~/.bash_aliases
	f.add		
		alias mpy_cross='$HOME/micropython/mpy-cross/./mpy-cross'

	   save&exit
```

4. Compile a micropython port for esp32
	
	### Method 1
```sh
	a.$cd $HOME/micropython/port/esp32
	b.edit mpconfigort.h  (if necessary add/del internal modules)
	c.$esp32_idf (must be called before every compilation if native C files included)
	d.$make       (perform all actions in one terminal window)
```

	As a result of the assembly, the firmware file.bin will be in the $HOME/micropython/port/esp32/build-Generic folder. 
	It consists of three bin-files :  partition / bootloader / micropiton .
	In esp32 we will upload the firmware.bin
	
	********Attention*******
	30 semptember 2021 one certificate in the cacrt_all.pem file has expired
		
	Temporary solution:
	* Edit  /micropython/ports/esp32/build-GENERIC/sdkconfig:
```sh
			CONFIG_MBEDTLS_CERTIFICATE_BUNDLE=n  (instead of "y")
			CONFIG_MBEDTLS_CERTIFICATE_BUNDLE_DEFAULT_FULL=n  (instead of "y")
```

in esp32 repository has already new issue ##7660 for this problem.

	
	### Method 2
```sh
	a.copy build-esp32-latest.sh from $HOME/micropython/tools/autobuild/ to $HOME/micropython/port/esp32
	b. $./ build-esp32-latest.sh  esp32_idf GENERIC ~/micropython/port/esp32 
```
	
	In this case, all precompiled files from which the final image is assembled will be placed in the build-Generic folder.
	
	
Documents:
https://docs.micropython.org/en/latest/develop/natmod.html
https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/


###How to compile .mpy from native C and/or py files instructions:

*tested in ubuntu 20.04 image as virtual machine*

```sh
	a. $sudo apt-get install git wget flex bison gperf python3 python3-pip python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util
	b. $sudo apt-get install python-is-python3 python3-pip python3-setuptools
	c. $pip3 install pyelftools>=0.25
```

2.install ESP32 toolchain 
```sh
	a. $mkdir esp
	b. $cd esp
	c. $git clone -b v4.2 --recursive https://github.com/espressif/esp-idf.git
	d. $cd esp-idf
	e. $./install.sh
	f. $nano ~/.bash_aliases
	g. add
		alias esp32_idf='. $HOME/esp/esp-idf/export.sh'

	   save&exit
```

3. Download micropython resp and compile mpy-cross compiler
```sh
	a.$cd $HOME
	b.$git clone https://github.com/micropython/micropython.git
	c.$cd micropython/mpy-cross
	d.$make
	e.$nano ~/.bash_aliases
	f.add		
		alias mpy_cross='$HOME/micropython/mpy-cross/./mpy-cross'

	   save&exit
```

4. Compile a mpy-fyles
```sh
	a.$cd $HOME/micropython/examples/natmod/features0
	b.edit Makefile  (MOD = mpy_modul's_name, ARCH = xtensawin)
	c.$esp32_idf (must be called before every compilation if native C files included)
	d.$make       (perform all actions in one terminal window)
```

Documents:
https://docs.micropython.org/en/latest/develop/natmod.html
https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/