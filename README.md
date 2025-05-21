# Wiring

![Raspberry Pi Pinout](pi%20pinout.png)

# Raspberry Pi Configuration

On the terminal, enter raspi-config as sudo:

```bash
sudo raspi-config
```

Now allow the Pi to use UART hardware:

```
3 Interface Option -> I6 Serial Port
```

- `Would you like a login shell to be accessible over serial?` **NO**
- `Would you like the serial port hardware to be enabled?` **YES**

Exit raspi-config saving changes and reboot the Pi.

Now edit `/boot/config.txt`:

```bash
sudo nano /boot/config.txt
```

If any of these lines are missing, add them:

```bash
enable_uart=1
dtoverlay=disable-bt
dtoverlay=uart1
```

**Note:** If you have `dtoverlay=uart0`, I suggest changing it to `uart1`. I encountered different bugs using `uart0`.

Reboot again and Raspberry Pi OS settings are done.

# Firmware Building

Head to your Klipper folder, usually in your home folder:

```bash
cd klipper/
```

Open make settings:

```bash
make menuconfig
```

Enable low level configuration:

![Low Level Configuration](Pasted%20image%2020250521212305.png)

Select USART 3 on PB11/PB10 as your `Communication interface`:

![Communication Interface](Pasted%20image%2020250521212354.png)

Choose a humane baud rate:

![Baud Rate](Pasted%20image%2020250521212808.png)

Build the firmware:

```bash
make
```

You will find the compiled firmware at `~/klipper/out/klipper.bin`. Flash this to your board (explaining how to flash the firmware is beyond the scope of this guide).

# Klipper Settings

This boils down to just defining correctly the `[mcu]` setting on `printer.cfg`:

```config
[mcu]
serial: /dev/serial1
baud: 115200
#serial: /dev/serial/by-id/usb-1a86_USB_Serial-if00-port0
restart_method: command
```

Make sure `serial` is set to `/dev/serial1` if you are using `uart1` (which I **STRONGLY** suggest) on the Raspberry Pi config file.

# Screen Unavailability

Keep in mind that now pins `PB10` and `PB11` of the microcontroller are reserved for serial interfacing with the Raspberry Pi, so you might need to delete or comment your `[display]` section in `printer.cfg`:

```config
#[display] # RET6 12864 LCD
#lcd_type: st7920
#cs_pin: PB12
#sclk_pin: PB13
#sid_pin: PB15
#encoder_pins: ^PB14, ^PB10        # PB10 now used as serial
#click_pin: ^!PB2
```
