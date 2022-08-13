Hi!
I just wanted to share my config files for the ENDER 3 MAX with a SKR Mini E3 V3 mainboard and a CR-Touch with z-homing.
Feel free to use these files as you please, always at your own risk! 

It's highly recommended that you stay close to the PSU power switch when you try the firmware for the first time in case something unforseen happens!

Please make sure that your fans are connected as shown below:

FAN0=Part Cooling	

FAN1=Hot-End			

FAN2=Control-board/Mainboard


Currently the firmware uses 36 probe points for auto bed leveling. I've upped the homing feedrate a bit as I'm using dual z-axis setup. 
The original values are commented out so you can easily roll back the changes I've made or do your own adjustments.

The serial port should work just fine so you can do a PID autotune for the hotend & bed (with pronterface for example). The process is
fairly easy, takes about 20 minutes, and is highly recommended as it can and often will improve the print quality.  ( https://marlinfw.org/docs/gcode/M303.html )

Thermal runaway protection is active!

You might want to calibrate your e-steps aswell while you're up to it. ( https://3dprintbeginner.com/extruder-calibration-guide/ )
Otherwise you should change the e-steps back to crealitys standard value(93) if you're using the stock extruder.




what I've changed in config.h:


I've changed the DEFAULT_ACCELERATION; DEFAULT_RETRACT_ACCELERATION; DEFAULT_TRAVEL_ACCELERATION as well. The original values are commented out.

If you have a BL/CR-Touch you should check the NOZZLE_TO_PROBE_OFFSET values ( configuration.h), as well as the XY_PROBE_FEEDRATE and Z_PROBE_FEEDRATE_FAST.
Original values, yadda yadda...

If a stepper is not moving in the right direction, search for INVERT_X_DIR or INVERT_Y_DIR etc.

If you want a bigger or smaller grid for ABL you can chance the GRID_MAX_POINTS_X value. 

DON'T change the bed size! It needs to be 305 x 305 (or smaller) or otherwise it'll crash during homing. 

Bed tramming assistant is active. 

Z_SAFE_HOMING is enabled and the HOMING_FEEDRATE_MM_M has been slightly adjusted. 

If you don't use the stock ENDER 3 MAX blue/white display you'll have to make changes in the [LCD / Controller Selection].
(The stock ender display is referred to as CR10_STOCKDISPLAY in the config.h)





These were the most important changes that I've made to the configuration.h so let's take a look at configuration_adv.h:


I had to disable the POWER_LOSS_RECOVERY feature as it was randomly triggered when the printer was printing for about 20 minutes.

I've enabled the controller fan and set the CONTROLLERFAN_SPEED_MIN to 64 so it's slowly spinning in stand by.
Define CONTROLLER_FAN_PIN PB15 to control the mainboard(FAN2) fan.

Then I've defined FAN_MIN_PWM 64 which equals to 25% as I currently have problems to get both part cooling fans spinning reliably
below 20%. That's why they're either off or start directy at 25% speed. 
I suspect, that's because both part cooling fans are connected to a single output on the board.

Then we need to activate FAST_PWM_FAN to set the hardware PWM for the fans. Otherwise they'll be whining audibly.
I've just left it at the standard value(31.4kHz) for 16MHz boards as the calculated value(125.490kHz) for 64MHz boards causes an error in PlatformIO.
(Yet I wouldn't be surprised if my calculations aren't correct) 

After that, we take a quick look into Marlin -> pins -> stm32g0 -> pins_BTT_SKR_MINI_E3_V3_0.h (located in the sidebar on the left)
to find out which pins we need for the hot-end(PC7) and part cooling(PC6). 

I've changed EXTRUDER_AUTO_FAN_TEMPERATURE to 40°C instead of 50°C. Neglible. 

Check the BL-Touch settings if anything needs to be changed for your probe specifically. Currently working fine with a CR-Touch.

You might want to disable BLTOUCH_HS_MODE (high speed mode) if you have probing errors.

G34 or Z_STEPPER_AUTO_ALIGN can't be used to automatically align your z-steppers because the board shares a single driver for the z-axes.

You could make use of XY_FREQUENCY_LIMIT ! I haven't tested this feature yet but it does sound useful for cartesian printers,
especially for the Ender Max with the fairly heavy and big bed.

ADAPTIVE_STEP_SMOOTHING was activated. Deactivate this feature if your prints shift layers.

I've activated the PROBE_OFFSET_WIZARD in line 1292. Deactivate it, if you don't need this feature.

Also the PREHEAT_SHORTCUT_MENU_ITEM was activated to have quick access to the preheating-menu.

Then LCD_SET_PROGRESS_MANUALLY is activated so we can make use of USE_M73_REMAINING_TIME a few lines below. 
This displays the actual remaining time instead of an estimation. (Which may give out false numbers if your first layer(s) are slowed down)

LONG_FILENAME_HOST_SUPPORT as well as SCROLL_LONG_FILENAMES were defined so I don't have to rename those crazy long .stl files.

Enabled BABYSTEPPING, you can access it from the home screen by pressing the knob twice, works during printing aswell. 
Baby

Haven't touched the "Advanced Pause for Filament Change" section, thats up to you ;)

Then I've copied the HAS_TRINAMIC_CONFIG section from the BTT config_adv.h 

and thats about it for config_adv.h
Hope this information was at least partially helpful.

- Sergeant Stoned











