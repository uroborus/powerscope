## **PowerScope** ##

The Power Scope is a meter intended for use on a breadboard to aid in the development of battery-operated devices.  It provides dedicated monitoring at the power source so you can quickly see the effects of circuit/software changes on power consumption during development.   This frees up your DVM for general diagnostic use.

The PowerScope is able to sample voltage and current, and compute the power draw, at a 1kHz rate. This allows it to accurately compute average power consumption in devices where portions of the circuit are periodically shut down to conserve power, such as sensor controllers.  The display is updated once per second, and power is averaged over a five second interval.<br><br>


<img src='http://farm9.staticflickr.com/8499/8410539966_c347bcab1f.jpg' />
<blockquote>PowerScope Layout<br><br><br></blockquote>

<h3>PowerScope Specs</h3>
<ul><li>Voltage measured from 0-10.0vdc at .01v resolution<br>
</li><li>Current measured 0-300.0mA at .01mA resolution (High Range)<br>
</li><li>Current measured 0-300.0uA at .01uA resolution (Low Range )<br>
</li><li>Reverse voltage protected<br>
</li><li>Maximum Burden Voltage = 0.3v<br>
</li><li>Meter itself consumes 8.5mA from a 5.4v supply<br>
</li><li>Power source can be +3.5v to +10.0vdc<br>
</li><li>Inputs are Overvoltage protected up to +/- 40.0vdc<br>
</li><li>Sample update rate = 1khz<br>
</li><li>Voltage zeroing and calibration functions via function buttons<br>
</li><li>Current zeroing and calibration functions for mA and uA ranges<br>
</li><li>Current measurement is done "low side" on circuit ground return path<br>
</li><li>Dimensions 1.0” x 2.6”<br>
<br><br><br></li></ul>

<img src='http://farm9.staticflickr.com/8188/8385746864_ae379d9fac.jpg' />

Measuring power consumption of a 555 timer circuit generating a 100Hz signal<br>Here the PowerScope is powered from the same 5v supply as the circuit under test.<br>
With header pins soldered on the back of the board at the IN/OUT connections, the<br> PowerScope can be plugged directly into the solderless breadboard.<br><br><br>

<img src='http://farm9.staticflickr.com/8097/8384663065_4b50fe48d6.jpg' />

Measuring power drawn by a T.I. MSP430G2211 processor in low-power mode 4. <br>
Chip spec states nominal .1uA consumption.  Here the PowerScope is powered<br> from the 5v source, but it measures the 3.3v regulator output used to power the processor.<br><br>

NOTE:  When testing the circuit above, you must start with the meter in high range and then switch to low range when the processor transitions to low-power mode.   Make sure to use at least a 1-5uF bypass capacitor on the output of the PowerScope.  Since the range switch is a "break before make" type,  the capacitor is needed to supply power to the circuit during the few milliseconds that it takes to flip the switch from high to low range.  <br><br>

<h3>Programming</h3>
The PowerScope has a T.I MSP430G2402 microprocessor programmed in C using the Code Composer Studio.  Program ROM is flashed using a T.I. LaunchPad, connected to a Windows PC via USB cable.  A four-wire cable is used to connect the Launchpad to the four programming pads on the upper corner of the board. <br><br>

<img src='http://farm9.staticflickr.com/8092/8501246787_4f1d907d7c_b.jpg' />

Note the unused pads next to the processor chip.  They are there to add a crystal for more precise time measurement if a user wants to program a display of cumulative mA-hours, for example.  <br><br>

Warning:<br>
<blockquote>The PCB is designed to accept 1206 size surface-mount resistors for the two shunt resistors of 1 ohm and 1000 ohm, nominally 1/4 watt rated.  If the OUT power connection were shorted to ground, or connected to load of less than 20 ohms, while switched to the mA range,  the 1-ohm shunt resistor could be damaged.  <br><br></blockquote>

<h3>Connections</h3>

The PoweScope can be powered from the same supply as the circuit,  or from a separate source, as shown below:<br>

<img src='http://farm9.staticflickr.com/8517/8581288714_d055c5363a_b.jpg' />

<h3>Calibration</h3>

Calibration is done in the software to avoid the need for tight-tolerance components.<br>
<br>
Voltage and current measurements are calibrated and zeroed using the function buttons.  A trusted multimeter can be used to calibrate volts and mA by connecting a power source to a resistor and measuring the current flow with both meters.    Then, a high-value precision resistor can be used with a voltage source to calibrate the uA current range at values too small for the multimeter.<br>
<br>
The mode button selects which value to zero/calibrate, then the up(+)/down(-) buttons will make adjustments.   The measured value in the upper right will change in real time.   The calibration constant used in the software is displayed on the lower right.   The range switch position determines whether the mA or uA range is being calibrated. When you exit calibration mode, the calibration constants are saved in the “Info” section of the MSP430 flash ROM.<br>
<br>
<img src='http://farm9.staticflickr.com/8242/8506201542_50579394ed_n.jpg' />

<img src='http://farm9.staticflickr.com/8384/8505092129_275aef39ce_n.jpg' />

The calibration constants are multipliers used in the code to adjust values to compensate for variances in the component values.   If you find you need to adjust the calibration to where the constant is above 1200 or below 800, you might want to look at the shunt resistors or the instrumentation amp gain resistor for damage or out-of-tolerance condition.<br>
<br>
<h3>Shunt Resistor Options</h3>

The PowerScope has been tested with lower shunt resistors of .1ohm for mA range and 100 ohm for uA range.  The advantage of the lower shunts is that it drops the maximum burden voltage from 300mV to 30mV.<br>

The contact resistance of the switch becomes significant when using these<br>
shunt values, since the measurement is done across both the resistor and the switch<br>
on the PowerScope.  Your switch's contact resistance may exceed the manufacturer spec and make it hard to calibrate the unit without changing the source code.  See this post:<br>
<br>
<a href='http://forum.43oh.com/topic/3430-powerscope/?p=36401'>http://forum.43oh.com/topic/3430-powerscope/?p=36401</a>

I recommend staying with the higher shunt values, but if you want to try the lower value shunts, there is a line you can un-comment in v1.1 of the code:<br>

// #define LOW_SHUNTS<br>
<br>
Then:<br>
<br>
Replace the 1.0 ohm shunt with .1 ohm (Digikey P100LPCT-ND)<br>
Replace the 1000.0 ohm shunt with 100.0 ohm (DigiKey P100FCT-ND)<br>
Replace the 54.9K gain resistor with 4.99k (DigiKey P4.99KCCT-ND)<br><br>

Questions about this project can be emailed to:<br>
<blockquote>gravitastech-email@yahoo.com</blockquote>
