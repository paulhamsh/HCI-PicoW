# HCI-PicoW
Access HCI layer of bluetooth on the Pico Pi W




```
git clone https://github.com/micropython/micropython.git
cd mircopython
make -C mpy-cross
cd ports/rp2
make BOARD=RPI_PICO_W submodules
make BOARD=RPI_PICO_W clean
make BOARD=RPI_PICO_W

Copy *.uf2 to somewhere where you can drag to the Pico W
```
