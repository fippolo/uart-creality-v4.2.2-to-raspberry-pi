# Wiring

![[pi pinout.png]]
# raspberry pi configuration:

on the terminal enter raspi-config as sudo:
```bash
sudo raspi-config
```
now allow the pi to use uart hardware
```
3 Interface Option -> I6 Serial Port
```
	`would you like a login shell to be accessible over serial?  NO`
	`Would you like the serial port hardware to be enabled? YES`

exit raspi-config saving changes and reboot the pi

now edit /boot/config.txt
```bash
sudo nano /boot/config.txt
```

if any of these line are missing add them:
```bash
enable_uart=1
dtoverlay=disable-bt
dtoverlay=uart1
```
Note: if you have dtoverlay=uart0 i suggest changing it to uart1, i encountered different bugs using uart0

reboot again and raspberry pi os settings are done

# Firmware building

head to your klipper folder, usually in your home folder
```bash
cd klipper/
```

open make settings
```bash
make menuconfig
```

enable low level configuration
![[Pasted image 20250521212305.png]]

select usart 3 on pb11/pb10 as your `Comunication interface`
![[Pasted image 20250521212354.png]]

choose a humane baud rate:
![[Pasted image 20250521212808.png]]

build the firmware
```bash
make
```

you will find the compiled firmware at  `~/klipper/out/klipper.bin` flash this to your board (explaining how to flash the firmware is beyond the scope of this guide)

# klipper settings

this boils down to just defing correctly the `[mcu]` setting on  `printer.cfg`

```config
[mcu]
serial: /dev/serial1
Baud: 115200
#serial: /dev/serial/by-id/usb-1a86_USB_Serial-if00-port0
Restart_method: command
```

make sure `serial` is set to `/dev/serial1` if you are using `uart1` (wich i STRONGLY suggest) on the raspberry pi config file

# Screen unavailability 

keep in mind that now pin `PB10` and `PB11` of the microcontroller are now reserved for serial interfacing with the raspberry pi so you might need to delete or comment your `[display]` section in printer.cfg

```config
#[display] # RET6 12864 LCD
#lcd_type: st7920
#cs_pin: PB12
#sclk_pin: PB13
#sid_pin: PB15
#encoder_pins: ^PB14, ^PB10        --------- now used as serial
#click_pin: ^!PB2 
```