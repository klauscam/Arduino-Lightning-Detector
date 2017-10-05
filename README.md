Lightning Detector
=========

In this tutorial, we will build a lightning detector using an Arduino Uno, a few resistors and some jumper wires. Most lightning detectors often cost too much for the normal hobbyist, however this does not mean one cannot enjoy lightning detection and the physics behind it. In this tutorial, using a surprisingly simple circuit we will be able to detect lightnings from around 10-20 km away, which is to say the least impressive.

The aim is to construct a simple circuit to detect lightnings with an Arduino and produce meaningful results.

Background
=====

When a lightning strikes, a huge amount of energy is released in different forms. The most obvious are light and sound, the latter being a by-product of the rate of temperature increase of the immediate particles surrounding the lightning bolt, which then causes the sound. 

But, that is not all. Lightnings emit large amount of electromagnetic radiation in the VLF (Very Low Frequency) and LF (Low Frequency) range, typically ranging from 3kHz to 300kHz. VLF and LF are similar to light waves, your WiFi waves and also your microwave oven waves, but with the difference of operating at lower frequencies. eg. WiFi normally operates at around 2.4GHz, that’s 2.4 billion oscillations per second. VLF and LF operates at lower frequencies, and with an Arduino we can capture frequencies around 7kHz. The advantages in using this kind of radiation for lightning detection is that normally nothing gives out large bursts as seen in lightnings, around this frequency; and being an electromagnetic wave it travels at the speed of light, which means the sensor will detect lightnings as they happen (a few micro seconds after).

Our little Arduino will have an antenna (sort of), a piece of wire that will pick fluctuations in electromagnetic spectrum specifically around the 7-9kHz. These fluctuations will induce a small voltage +ve or -ve in the wire. We can pick these fluctuations using Arduino’s analog pins.

Prerequisites
=======
    2x10k Ohms Resistor
    1x 3.3M Ohms Resistor
    4x Jumper Wires
    1x Arduino (I’m using Uno but any other will work as long as it can operate at 16Mhz)
    Breadboard for simplicity 🙂

As you might already know, the pins on the Arduino board allow for voltages between 0v and 5v, anything below 0v and above 5v will not be read,hence data will be lost. 
More importantly, voltages below 0v will potentially damage the pin. This will create a little problem for us because the voltages produced in the wire fluctuate below and above 0v. To solve this problem we set the pin voltage in the middle of the 5v range, at 2.5v and this will be accomplished using a little trick, a voltage divider. In doing so, we will be setting the pin to a steady 2.5v and the voltage fluctuations will have an origin of 2.5v, hence no damage or loss of data.

![Diagram 1](https://github.com/klauscam/Arduino-Lightning-Detector/blob/master/images/lightningdetector.png)

![Diagram 2](https://github.com/klauscam/Arduino-Lightning-Detector/blob/master/images/lightningdetector_schem-1-e1466701621717-768x851.png)


The circuit is pretty straight forward, we have 2x 10k Ohm resistors in series from 5v (red wire) to GND (black wire), this is basically the voltage divider. Then a 3.3M Ohm (MegaOhm) resistor is connected between the 2x 10k Ohm resistor. In series with the 3.3M Ohm resistor attach a wire to pin A4 (blue wire), this will give us exactly 2.5v on pin A4. Then attach a wire which will act as an antenna (green wire) of around 6-8 inch in length. This should be connected from one end only as shown above.

Sketch
===

Here comes the hardest part to explain. As mentioned above, the frequency we need to pick up from the lightnings, is around 7kHz and to read a semi-decent wave the sample rate has to be 4x as much, giving us 4 readings per wavelength. That is, 28,000 samples per second.

The Arduino analog pins can only give us 9,600 samples per seconds. With that sample rate we will only be able to capture waves at 2kHz or a bit more, which is far from good. Thanks to the ATMEGA chip, it can be configured to speed up the ADC process by a certain factor, while retaining a good resolution. This is called the prescaler, and can be configured through code. There are a number of prescaler dividing factors but we will use factor 16 which in theory will give us a sampling rate of 77kHz. In practice any form of calculation will lower this sampling rate thus I was only able to get around 46kHz which is still very good for this project.

So moving forward, the sketch uses a 512 byte array to store voltage valves from pin A4. It constantly reads the pin value and writing it to the next location in the array. As soon as a lightning is detected the whole array is sent over the serial port. This can be plotted on the graph plotter in Arduino IDE or maybe sent over to another Arduino or ESP8266 to publish the data online. It’s probably best to monitor it via the Arduino IDE at first, so if there are some glitches, they can be tackled there and then.

Results
====
The following are some results.

![Result 1](https://github.com/klauscam/Arduino-Lightning-Detector/blob/master/images/lightningexmple-2.png)

![Result 2](https://github.com/klauscam/Arduino-Lightning-Detector/blob/master/images/lightningexmple-1.png)


Please feel free to comment below if you need any further clarifications.

-------
Update 2
====
To use the Arduino IDE serial plotter please replace this function

	void sendData()
	{
 		// Serial.print(">>>");
 		// Serial.println(batchStarted);
  		
  		for (int i=0;i<data;i++){
    		Serial.println(storage[i]);
  		}
 		// Serial.print("<<<");
 		// Serial.println(batchEnded);
 		// Serial.println("END");
  
  		toSend=false;
	}

-------
Update 3
===

Note that the device is very sensitive to EMF fluctuations. This includes AC power supplies. Place the device away from any AC supply and any socket outlets. We have also noticed that it behaves abnormal when connected to a laptop while charging. We recommend to use a laptop running on batteries only for optimum performance.
