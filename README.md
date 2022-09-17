
<img src ="images/photo_2022-03-31_22-41-40.jpg"><img src ="images/photo_2022-03-31_22-42-09.jpg">

# DWIN_T5UIC1_LCD

## Python class for the Ender 3 V2 LCD runing klipper3d with Moonraker 

https://www.klipper3d.org

https://octoprint.org/

https://github.com/arksine/moonraker


## Setup:

### [Disable Linux serial console](https://www.raspberrypi.org/documentation/configuration/uart.md)
  By default, the primary UART is assigned to the Linux console. If you wish to use the primary UART for other purposes, you must reconfigure Raspberry Pi OS. This can be done by using raspi-config:

  * Start raspi-config: `sudo raspi-config.`
  * Select option 3 - Interface Options.
  * Select option P6 - Serial Port.
  * At the prompt Would you like a login shell to be accessible over serial? answer 'No'
  * At the prompt Would you like the serial port hardware to be enabled? answer 'Yes'
  * Exit raspi-config and reboot the Pi for changes to take effect.
  
  For full instructions on how to use Device Tree overlays see [this page](https://www.raspberrypi.org/documentation/configuration/device-tree.md). 
  
  In brief, add a line to the `/boot/config.txt` file to apply a Device Tree overlay.
    
    dtoverlay=disable-bt

### [Enabling Klipper's API socket](https://www.klipper3d.org/API_Server.html)
  Check your /etc/systemd/system/klipper.service file for API enable parameter `-a /tmp/klippy_uds`:

  `ExecStart=/home/pi/klippy-env/bin/python /home/pi/klipper/klippy/klippy.py /home/pi/klipper_config/printer.cfg -l /home/pi/klipper_logs/klippy.log -a /tmp/klippy_uds`

### Library Requirements 

  Thanks to [wolfstlkr](https://www.reddit.com/r/ender3v2/comments/mdtjvk/octoprint_klipper_v2_lcd/gspae7y)

Install System Requirements

```shell
sudo apt-get install python3-pip python3-gpiozero python3-serial git
```

Install Python Requirements

```shell
sudo pip3 install multitimer
```
  
### Clone This Repo

```shell
git clone https://github.com/mkocot/DWIN_T5UIC1_LCD.git
```


### Wire The Display 
  * Display <-> Raspberry Pi GPIO BCM
  * 1  - Nc
  * 2  - Nc
  * 3  - Rx   = 8  - GPIO14  (Tx)
  * 4  - Tx   = 10 - GPIO15  (Rx)
  * 5  - Ent  = 33 - GPIO13
  * 6  - BEEP = 31 - GPIO6 (Optional)
  * 7  - A    = 35 - GPIO19
  * 8  - B    = 37 - GPIO26
  * 9  - Vcc  = 4  - (5v)
  * 10 - Gnd  = 6  - (GND)

Here's a diagram based on my color selection:

<img src ="images/GPIO.png?raw=true" width="325" height="75">
<img src ="images/panel.png?raw=true" width="325" height="180">

I tried to take some images to help out with this: You don't have to use the color of wiring that I used:

<img src ="images/wire1.png?raw=true" width="200" height="400"> <img src ="images/wire2.png?raw=true" width="200" height="400">

<img src ="images/wire3.png?raw=true" width="400" height="200">

<img src ="images/wire4.png?raw=true" width="400" height="300">

### Run The Code

Enter the downloaded DWIN_T5UIC1_LCD folder.
Make new file run.py and copy/paste in the following (pick one)

For an Ender3v2

```python
#!/usr/bin/env python3
from dwinlcd import DWIN_LCD

encoder_Pins = (26, 19)
button_Pin = 13
LCD_COM_Port = '/dev/ttyAMA0'
API_Key = 'XXXXXX'

DWINLCD = DWIN_LCD(
	LCD_COM_Port,
	encoder_Pins,
	button_Pin,
	API_Key
)
```

If your control wheel is reversed (Voxelab Aquila) use this instead.

```python
#!/usr/bin/env python3
from dwinlcd import DWIN_LCD

encoder_Pins = (19, 26)
button_Pin = 13
LCD_COM_Port = '/dev/ttyAMA0'
API_Key = 'XXXXXX'

DWINLCD = DWIN_LCD(
	LCD_COM_Port,
	encoder_Pins,
	button_Pin,
	API_Key
)
```

Run with `python3 ./run.py`

# Run at boot:

	Note: Delay of 30s after boot to allow webservices to settal.
	
	path of `run.py` is expected to be `/home/pi/DWIN_T5UIC1_LCD/run.py`
	
	
```shell
sudo chmod +x run.py  
sudo chmod +x simpleLCD.service  
sudo mv simpleLCD.service /lib/systemd/system/simpleLCD.service  
sudo chmod 644 /lib/systemd/system/simpleLCD.service  
sudo systemctl daemon-reload  
sudo systemctl enable simpleLCD.service
```

Reboot the Pi

```shell
sudo reboot
```

   
   

# Status:

## Notworking:
    * Save / Loding Preheat setting, hardcode on start can be changed in menu but will not retane on restart.
    * The Control: Motion Menu
