# HCI-PicoW
Access HCI layer of bluetooth on the Pico Pi W

Micropython C module to access the VHCI layer in the Pico Pi W.   
Creates a module HCI_PicoW   

```
>>> from HCI_PicoW import *
>>> bt = HCI()
>>> bt.readable()
False
>>> bt.send_raw(b'\x01\x03\x0C\x00') 
True
>>> bt.readable()
True
>>> bt.receive_raw()
b'\x04\x0e\x04\x01\x03\x0c\x00'
>>> bt.send_raw(b'\x01\x01\x10\x00') 
True
>>> bt.receive_raw()
b'\x04\x0e\x0c\x01\x01\x10\x00\x0b\x00\x00\x0b1\x01\x10#'
```



## References   
https://retrobrewcomputers.org/n8vem-pbwiki-archive/0/38301400/48860991/60678324/Part_E2_SDIO_Bluetooth_Type_A_Simplified_V1.00_060403TC.pdf
or download Part E2 Simplified from here:   
https://www.sdcard.org/downloads/pls/ 

## How to build in WSL
```
sudo bash
apt update
apt upgrade

# Micropython installation
cd ~
git clone https://github.com/micropython/micropython.git

cd mircopython

make -C mpy-cross

# Clone this repo into micropython codebase

cd ~
git clone https://github.com/paulhamsh/HCI-PicoW.git
cd HCI-PicoW

mkdir ~/micropython/userc
cp -r HCI-PicoW ~/micropython/userc

# Build micropython with the user module
cd ~/micropython/ports/rp2

make BOARD=RPI_PICO_W submodules
make BOARD=RPI_PICO_W clean
make BOARD=RPI_PICO_W USER_C_MODULES=~/micropython/userc/HCI-PicoW/micropython.cmake
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


## Why doesn't it crash if BTStack is not initialised?

If we don't start BTSTack within Micropython, then why is it ok to allow the handler to run (see https://github.com/paulhamsh/HCI-PicoW-C).

The handler is here (in pico-sdk/src/rp2_common/pico_cyw43_driver/btstack_hci_transport_cyw43.c):

```
// This is called from cyw43_poll_func.
void cyw43_bluetooth_hci_process(void) {
    if (hci_transport_ready) {
        btstack_run_loop_poll_data_sources_from_irq();
    }
}
```

As you can see, it checks ```hci_transport_ready``` which is defined  in pico-sdk/src/rp2_common/pico_cyw43_driver/btstack_hci_transport_cyw43.c   
If ```hci_transport_cyw43_open``` is not called then this value is false, which means that ```btstack_run_loop_poll_data_sources_from_irq``` is never called.   

To prove this, change ```static bool hci_transport_ready``` to ```bool hci_transport_ready``` (in pico-sdk/src/rp2_common/pico_cyw43_driver/btstack_hci_transport_cyw43.c) then it can be accessed from Micropython code.   
You can then tell that it isn't set to ```true``` unless BTSTack is invoked by Micropython, so the handlers are never called and the code is fine.   
The easy thing is to change ```readable``` in this program to return the value of this flag - as below.   

Perhaps it would be safer to explicitly set it to false in pico-sdk/src/rp2_common/pico_cyw43_driver/btstack_hci_transport_cyw43.c   

Change the code for readable in like this:
```
extern bool hci_transport_ready;

STATIC mp_obj_t HCI_readable(mp_obj_t self_in) {
    return mp_obj_new_bool(hci_transport_ready);
}
```

Then do this   

```
>>> from HCI_PicoW import *
>>> bt = HCI()
>>> bt.readable()
False
>>> bt.send_raw(b'\x01\x03\x0C\x00') 
True
>>> bt.readable()
False

>>> import bluetooth
>>> b = bluetooth.BLE()
>>> b.active(True)
>>> bt.readable()
True
>>> b.active(False)
>>> bt.readable()
False
```
