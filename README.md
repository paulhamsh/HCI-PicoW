# HCI-PicoW
Access HCI layer of bluetooth on the Pico Pi W

Micropython C module to access the VHCI layer in the ESP 32.   
Creates a module HCI_ESP32   

```
>>> from HCI_ESP32 import *
>>> bt = HCI()
>>> bt.readable()
False
>>> bt.send_raw(b'\x01\x03\x0C\x00') 
True
>>> bt.readable()
True
>>> bt.receive_raw()
b'\x04\x0e\x04\x05\x03\x0c\x00'
>>> bt.send_raw(b'\x01\x01\x10\x00') 
True
>>> bt.receive_raw()
b'\x04\x0e\x0c\x05\x01\x10\x00\x08\x0e\x03\x08`\x00\x0e\x03'
```



# References   
Micropython instructions: https://github.com/micropython/micropython/blob/master/ports/esp32/README.md    
usbipd-win instructions: https://devblogs.microsoft.com/commandline/connecting-usb-devices-to-wsl/


# Build instructions (WSL on Windows)
Install USBIPD (provide access to ESP32 over USB into WSL) from  https://github.com/dorssel/usbipd-win/releases    
Presumes WSL already active on your Windows machine
 
# When in WSL

sudo bash
apt update
apt upgrade

# Micropython installation
cd ~
git clone https://github.com/micropython/micropython.git
cd mircopython

make -C mpy-cross

cd ports/rp2

make BOARD=RPI_PICO_W submodules
make BOARD=RPI_PICO_W clean
make BOARD=RPI_PICO_W


# Clone this repo into micropython codebase

cd ~
git clone https://github.com/paulhamsh/HCI-PicoW.git
cd HCI-PicoW

mkdir ~/micropython/userc
cp -r HCI-PicoW ~/micropython/userc

cd ~/micropython/ports/rp2
make USER_C_MODULES=~/micropython/userc/HCI-PicoW/micropython.cmake
````

Copy *.uf2 to somewhere where you can drag to the Pico W, like:

````
cp build-RPI_PICO_W/firmware.uf2 /mnt/c/users/paul/Desktop
````
Then drag and drop in Windows    




## Notes
Micropython includes a Pico SDK here:
```micropython/lib/pcio-sdk```

You an delete it an replace with SDK direct from raspberry pi if you want.   

The driver for CYW43xxxx is here
```micropython/lib/pico-sdk/src/rp2_common/pico_cyw43_driver```
and the fun happens in here
```btstack_hci_transport_cyw43.c```


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
