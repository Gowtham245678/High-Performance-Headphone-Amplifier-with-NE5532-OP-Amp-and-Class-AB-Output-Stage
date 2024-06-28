This repository is dedicated for the Headphone amplifier project. In this repository you are going to find the schematic of the project, a pcb report document and a folder with the manufacturing files of the pcb. We’re also going to talk about the initial requirements I had with the design, the reasons I choose to use the final circuit topology, the components I used in this Project.
![Screenshot 2024-06-29 003056](https://github.com/Gowtham245678/High-Performance-Headphone-Amplifier-with-NE5532-OP-Amp-and-Class-AB-Output-Stage/assets/138134146/fdc01ef9-8865-4b0d-9197-bd1bbdae3e84)

Starting of with the requirements, I wanted to create a headphone amplifier that it could be powered up from a single 20V power supply. The reasons I came up with the 1st requirement were that I wanted to create a low cost device that can be powered up from a 20v SMPS power supply, a very common device used to power up electronics that anybody could have laying around. So this will replace the dual supply rails created by a transformer, a few diodes and filter caps which add a significant cost. As cheap SMPS are known to create noisy power, it was important to add some linear regulation and some additional filter caps to step the voltage from 20V down to 17-17.5V so we can have a somewhat better quality voltage supply. The 2nd requirement I had was that wanted to implement a hybrid design that uses an op amp to do the voltage amplification and a discrete output stage to drive the headphones. Continuing, I wanted the amp to able to easily drive low impedance headphones across the audio range so I had to figure out the the ideal topology of the Class AB output stage. In the 3rd requirement, I wanted the amp to have high gain at 20db(10x) because although there are a few devices that output line level audio signal, there are still a lot of devices that output 0.3Vrms and without gain they can’t fully utilize the amplifier's power. Finaly, I wanted implement passive volume control that uses a pot to attenuate the input signal so after the pot, the signal needs to see high impedance for the pot resistance to dominate and to have linear attenuation.

To sum up:

20V SMPS
2W Peak Power
Op amp/Class AB Output Stage
20db Gain @20-20khz
Ideal for low impedance headphones
Passive volume control

The amp being single supply, we first need to bias up the input of the op amp to half of the supply rails so I used 2 100k resistor voltage divider that give a dc offset to signal of around 8.5-9V, also form the high impedance that we wanted and the signal sees impedance of 50k Omhs. Wanting to block the dc offset getting into our source, the 330n capacitor at the input is used for that and also forms the main high pass filter with cut off 10hz@-3db. For the volume control I chose a 20k linear pot which at low volumes its resistance will dominate and in max volume the input signal sees approx. 15k Omhs which is more than enough. I initially wanted to use one Opamp IC for both of the channels but in order to implement the low pass filter at 20khz we need to buffer the input because if we place it at the input, the variable series resistance of the pot together with the high pass filter resistance and the capacitor will change the response at different volumes. So the output resistance of the omp amp(NE5532 has appox. 7.5) together with the 20 omhs resistor and a 330n Capacitor create a cut off at 24khz.

After, the signal is outputted from the second opamp into the output stage. The initial plan was to use 5k resistors, two diodes with a resistor at the middle that bias up the two transistors of the class AB output stage. Testing the circuit in my bench, I noticed that this topology was fine for high resistance loads but it struggled at low impedances and the signal swing was reduced but I also had thermal issues with the output transistors because of the active biasing. To solve the thermal issue I replace the diodes-resistor bias circuit with a Vbe multiplier that will be placed close to the output resistors and in case the temperature of the output increases, it will turn the bias down and we’re gonna have that thermal feedback. I configured the Vbe multiplier at 1.3V just in the point the output stage starts to conduct less than 10mA. The signal is put from the opamp at the collector because at that point we have symmetrical clipping and therefore more swing than putting it at the emitter. As for the load problem, the current gain of the output stage was not enough to drive low impedance load so i had to implement a driver stage. I first went ahead with a Darlington configuration but although I solve the load problem the addition another transistor to the signal path reduced the swing. Having 17V available, the almost 4V loses of the opamp, the 3V loses from the Darlington pairs and few millivolts drop from the output resistors leave around 9Vpk-pk for the signal to swing which is less that 1W per channel even on low impedance loads. That’s why at the end I opted to use the Sziklai - complementary feedback pair to get that 1.5V pk-pk swing, reduced by the Darlington configuration. Because the output signal has that DC offset, a coupling cap is needed to get the final signal which also acts as a headphone protection in case one of the output transistors shorts and we have dc at the output. Note that this capacitor has to be big enough because together with load forms a high pass filter, and if is not, it will alter the bass response of the amp at the wide variety of the different headphone impedances. Finaly, I applied global feedback to get the required gain which also reduces distortions created by the output stage.
[Schematic_Headphone-amplifier_2024-06-28.pdf](https://github.com/user-attachments/files/16035832/Schematic_Headphone-amplifier_2024-06-28.pdf)

