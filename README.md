<h1 align="center">ESP32 micropython & webREPL installation guide</h1>
<h2 align="center">

<с align="left">
1. [Introduction] [Introduction]
</c>

<p align="center">
# [Introduction]: Introduction
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
