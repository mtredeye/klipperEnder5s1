# klipperEnder5s1
Ender 5S1 Klipper Settings 

This is my settings that after 4 days of learning Klipper (big change from Marlin) and lots of forums, I decided to put everything in one place to help out those new to Klipper and Ender5S1

My final configuration is a Pi3b with a 7" screen and the Ender5S1

Running Moonsail, MoonRaker, Klipper, KlipperScreen, and Cura

The forums I read through were:

https://www.reddit.com/r/ender5/comments/118v3eu/klipper_make_config_settings_for_ender_5_s1/

https://github.com/alxthedesigner/ender5-s1_klipper

https://github.com/dw-0/kiauh

I orginally planned on using an old touchscreen laptop using WSL2 running Debian, but after many hours I gave up...there was a lot to making it work, and things that had to be done everytime the laptop rebooted and it was just not a consistant platfrom.

Reading though different forums, I followed Rolohaun's 3 part video on youtube here is the link to the first one: https://www.youtube.com/watch?v=OmBIHB9TFgc

I chased trying to get my hostname to populate through my router to no avail...and ended up adding its ip to my hosts file on my Windows 11

There are plenty of how to's on that, I found my IP on the router and went from there...you can just use the ip its up to you....

Once you have everything installed and get to the part of building your bin file for the Ender5S1 I used the settings that were in the .cfg file I used from https://github.com/alxthedesigner/ender5-s1_klipper

Which was: 
STM32F401
64Kib bootloader
serial (on USART1 PA10/PA9) - I have seen different selections here but based on the original cfg provided by Creality I went with this and had no issues connecting to the printer.

When you get to the second video he walks you through all this and exporting the bin to a sd card. 

I did create a STM32F4_UPDATE folder and put the bin file in it.

The video also shows you how to get the address of the printer on the Pi...which there is a tool to get it, I used two different boards in the process and both had the same address and your probably will too but if its different you will need to edit it in the [mcu] section of the printer.cfg

The screen I used (mostly based on price) was direct connect 7" touchscreen, I didnt get it until I was already using the printer and it was plug and play...I did have some permission issues but the troubleshooting on https://klipperscreen.readthedocs.io/en/latest/ under First Steps/Log got it working.

I do not recall where I found it but you do have to go into raspi config.

I used PuTTY to ssh to the raspberry

sudo raspi-config

Option 1 System Options

Option S5 Boot / Auto Login

Option B2 Console Autologin

You also need to upload a printer.cfg to Mainsail, I started with the one on https://github.com/alxthedesigner/ender5-s1_klipper but had quite a few changes I will discuss further down

Once I had all of that going it was time to connect it to Cura

In Cura go to the marketplace and download the Moonraker Connection

Add a new printer and you will see the option for connect to Moonraker

I did use my IP address here, hostname wasnt working and make sure you add your port which if you did everything default on the rapsberry install it would be 7125

So it should look like http://192.168.0.54:7125 where the .54 is the raspberry's ip

Nothing else needs to be done unless you want thumbnails when you send your prints (i did) so click on the Upload tab and select UFP with Thumbnail

I began with a intial settings in Cura based on https://the3dprinterbee.com/ender-5-s1-cura/

I did not use his Start and Stop Gcode and I set my wall speed at 60 mm/s

One of the coolest albiet difference with Klipper is GCode Macros...plenty of tutorials on those as well...I wanted to use a Macro and wanted it to pick up the settings from Cura for certain things...

In my printer.cfg there is the Start and Stop Macros that do just that and all you have to do is add the following into Cura

Go into your machine settings in Cura

For your Start Gcode delete everything and add:

SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=bed_temp VALUE={material_bed_temperature_layer_0}
SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=extruder_temp VALUE={material_print_temperature_layer_0}
START_PRINT

For your End Gcode delete everything and add:

SET_GCODE_VARIABLE MACRO=END_PRINT VARIABLE=machine_depth VALUE={machine_depth}
END_PRINT

****BEFORE YOU DO ANY MOVEMENT ON THE PRINTER****

You will want to set your bltouch x-offset and y-offset in the printer.cfg under [bltouch] mine were very different from the ones in the original printer.cfg

I aslo changed the samples from 1 to 2 after reading about errors when doing just 1 probe

You will also want to check your mesh_min and mesh_max under [bed_mesh] this determines the maximum area to probe

I also changed the probe count to 10,10 which is 100 points of probe and added mesh_pps 2,2 which extrapolates between the physical points

The next area to check is [screws_tilt_adjust]

This is where your bed screws are located, the original printer.cfg did not have this, there was a [bed_screws] which is the same data, I added the [screws_tilt_adjust] based on the documentation at https://www.klipper3d.org/Manual_Level.html

[bed screws] is when you dont have a probe and [screws_tilt_adjust] uses the probe...(Very Cool btw!)

Here is another reference how to use it from the klipperscreen

https://klipperscreen.readthedocs.io/en/latest/Panels/Screws/

From here I went to calibration...being a fan of TechingTech and using his calibration site for the Ender 3, I quickly found we are in a whole new world!

For that I found https://www.reddit.com/r/klippers/comments/z4pwcr/klipper_calibration_spreadsheet_a_downloadable/

Good video on youtube but the spreadsheet is the major help with links to the klipper documentation and how to's I learned a lot reading though that so until you go through this you will want to leave the items I have commented out in the printer.cfg and enable them as you go.

There were spome settings I changed from the original like shaper_type under [input_shaper] to mzv, as you will read the original was not compatable with using this macro...you will learn about that when go through the calibration

The one thing not covered in his guide is skew correction...highly recommend if you want dimensionally accurate prints. I followed this document for that https://www.klipper3d.org/Skew_Correction.html

I am really enjoying the Macros and Commands you can do with Klipper and will be adding more over time.



