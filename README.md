# DWIN_T5UIC1_LCD

https://github.com/RobRobM/DWIN_T5UIC1_LCD_E3S1

To get your API key run:

~/moonraker/scripts/fetch-apikey.sh


The first thing to do is to go in to you moonraker.conf file and find the line that starts with:

klippy_uds_address:
Edit that to look like this:

klippy_uds_address: /home/pi/printer_data/comms/klippy.sock
Just save and exit after that.

The second thing you need to do is open up putty and ssh in to you klipper server. when using Fluid or Mainsail uou need to go into the klipper.service file to enable the Klipper API Socket. You can do this by writing this:

sudo nano /etc/systemd/system/klipper.service
If the file has this line:

KLIPPY_ARGS="/home/pi/klipper/klippy/klippy.py /home/pi/printer.cfg -l /tmp/klippy.log"
Replace that with this line instead if it dosent just paste this in:

KLIPPER_ARGS="/home/pi/klipper/klippy/klippy.py /home/pi/printer_data/config/printer.cfg -I /home/pi/printer_data/comms/klippy.serial -l /home/pi/printer_data/logs/klippy.log -a /home/pi/printer_data/comms/klippy.sock"
After that you can follow this tutorial DWIN_T5UIC1_LCD untill you get to the Run The Code after that get back here and continue from here.

Make sure you have the screen in the right oration hade mine upside down the first time. It should power upp with the creality logo on it if done right.

Now you will need to edit some files. First write:

cd  DWIN_T5UIC1_LCD 
Now you should be in the right file directory so start with typing this:

sudo nano  printerInterface.py
Go down to line 256 and edit that to look like this:

Note if unsure what line you are on use CTRL + C it will show you what line you are on

self.ks = KlippySocket('/home/pi/printer_data/comms/klippy.sock', callback=self.klippy_callback)
Now you should save and exit and run this command:

sudo nano simpleLCD.service
In there under [Service] you should add this line:

Restart=always
Save and exit.

If you have a TJC you will need to flash cutom firmware that can be found here  https://github.com/mriscoc/Ender3V2S1/wiki/How-to-update-the-display. You can also follow that guide if you are unsure what screen you have and how to flash it.

Now if you have done every step go back to the DWIN_T5UIC1_LCD and continue the tutorial where you left of untill the end. Now you should have the V2 LCD upp and running with Klipper.

The screen is very finicky and sometimes stops working with firmware restart.

Edit: If tou find any spelling errors i am sorry english is not my native language and i am also dyslexic. But hope somebody find this useful.




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

### Library requirements 

  Thanks to [wolfstlkr](https://www.reddit.com/r/ender3v2/comments/mdtjvk/octoprint_klipper_v2_lcd/gspae7y)

  `sudo apt-get install python3-pip python3-gpiozero python3-serial git`

  `sudo pip3 install multitimer`

  `git clone https://github.com/SuperPi911/DWIN_T5UIC1_LCD.git`


### Wire the display 
  * Display <-> Raspberry Pi GPIO BCM
  * Rx  =   GPIO14  (Tx)
  * Tx  =   GPIO15  (Rx)
  * Ent =   GPIO13
  * A   =   GPIO19
  * B   =   GPIO26
  * Vcc =   2   (5v)
  * Gnd =   6   (GND)

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

   `sudo chmod +x run.py`
   
   `sudo chmod +x simpleLCD.service`
   
   `sudo mv simpleLCD.service /lib/systemd/system/simpleLCD.service`
   
   `sudo chmod 644 /lib/systemd/system/simpleLCD.service`
   
   `sudo systemctl daemon-reload`
   
   `sudo systemctl enable simpleLCD.service`
   
   `sudo reboot`
   
   

# Status:

## Working:

 Print Menu:
 
    * List / Print jobs from OctoPrint / Moonraker
    * Auto swiching from to Print Menu on job start / end.
    * Display Print time, Progress, Temps, and Job name.
    * Pause / Resume / Cancle Job
    * Tune Menu: Print speed & Temps

 Perpare Menu:
 
    * Move / Jog toolhead
    * Disable stepper
    * Auto Home
    * Z offset (PROBE_CALIBRATE)
    * Preheat
    * cooldown
 
 Info Menu
 
    * Shows printer info.

## Notworking:
    * Save / Loding Preheat setting, hardcode on start can be changed in menu but will not retane on restart.
    * The Control: Motion Menu
