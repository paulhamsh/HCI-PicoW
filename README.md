# HCI-PicoW
Access HCI layer of bluetooth on the Pico Pi W


Micropython includes a Pico SDK here:
```micropython/lib/pcio-sdk```

You an delete it an replace with SDK direct from raspberry pi if you want.   

The driver for CYW43xxxx is here: micropython/lib/pico-sdk/src/rp2_common/pico_cyw43_driver    


```
git clone https://github.com/micropython/micropython.git
cd mircopython
make -C mpy-cross
cd ports/rp2
make BOARD=RPI_PICO_W submodules
make BOARD=RPI_PICO_W clean
make BOARD=RPI_PICO_W

Copy *.uf2 to somewhere where you can drag to the Pico W, like:

cp build-RPI_PICO_W/firmware.uf2 /mnt/c/users/paul/Desktop


```
