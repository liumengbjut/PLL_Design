# **_Introduction to PLL_**
A Phase-locked loop (PLL) is a control system that generates an output signal whose phase is related to the phase of an input signal. The PLL block is a feedback control system that automatically adjusts the phase of a locally generated signal to match the phase of an input signal. The aim of using a PLL is to get a precise clock signal without frequency or phase noise and also have the flexibility of having the frequency of our choice. 

Voltage Controlled Oscillator and Quartz crystals are two ways where we can generate clock signals. The advantage of using VCO is that it has good flexibility but can be easily implemented on chip with inverters. We can control their frequencies using voltage. But they have fluctuations in their phase. Where as Quartz crystal have a higher spectral purity but are not flexible like VCO's. Thus the entire purpose of PLL is to make VCO mimic the Quartz crystal while maintaining flexibility. 

# **_Block Diagram_** 

![image](https://user-images.githubusercontent.com/54993262/127806194-cdc60451-60ac-483b-a73b-08bcf68c69be.png)
The PFD takes care of comparision between output signal and reference signal. Charge Pump converts digital waveform to analog waveform. The filter smoothens the CP output signal. The FD is the component which converts the whole system to a multiplier. Such a PLL is called a Clock Multiplier PLL as they are used in clock circuits to get different frequencies which are multiples of reference clock frequencies.  

# **_Phase Frequency Detector_**

![image](https://user-images.githubusercontent.com/54993262/127806480-584446af-38e1-4f62-b43c-3b47b4ef52c8.png)


By using an XOR gate, we can measure the phase difference. The width of the pulse can be a measure of phase difference. But XOR output would not change if the output was lagging, hence we cannot differentiate. 

![image](https://user-images.githubusercontent.com/54993262/127765493-f1f937e1-301d-4b2d-a5c1-46f7d0a40cd7.png)


For leading, we can look at DOWN signal and can slow down the output signal. Similarly from UP signal the output can be sped up.


![image](https://user-images.githubusercontent.com/54993262/127765590-a23805fc-18b4-43e8-a3cc-13f1cf973bf6.png)

For ref and output having different frequencies, it is as shown below:
![image](https://user-images.githubusercontent.com/54993262/127765665-8a9dba3f-b893-48bd-83df-fa6a848e93c7.png)
![image](https://user-images.githubusercontent.com/54993262/127765787-57a2aed0-8b67-41d1-ab0a-792067ae1e85.png)


This can also be represented by a state machine

![image](https://user-images.githubusercontent.com/54993262/127806585-87da6e18-76d7-466c-8c01-9364ecdce774.png)


The above state machine is implemented by a FF.

![image](https://user-images.githubusercontent.com/54993262/127765925-5d4ff697-38a6-48dd-ab73-933480f5678f.png)


These are negatively edged triggered FF. 2 FFs are needed as falling edge for 2 signals are to be detected. This is a popular PFD circuit, the only issue with this circuit is Dead Zone. Dead zone prevents us from improving the smallest difference in phase or freq that we can measure. The output here seems to be clipped as there is no time for it to raise 


# **_Charge Pump_** 


It converts the digital measure of phase/frequency into an analog control signal to control the oscillator. 

<p align="center">
<img src="https://user-images.githubusercontent.com/54993262/127766113-73c5e4e1-20db-4c42-ad1f-ea1367db6c83.png" width="300" height="400">
</p>



This is a Current Steering circuit. It directs the current flow from VDD to output or output to GND. If UP signal is active current flows from VDD to output and charges the capacitor. Thereby increasing voltage at CP output. If Down signal is active output flows from output to GND, thus discharges the output. 

If average Up signal active time is greater than down signal then it can be shown by the below graph. 


![image](https://user-images.githubusercontent.com/54993262/127766226-32284830-6edc-4e63-8a69-fea8c3bba826.png)

Else


![image](https://user-images.githubusercontent.com/54993262/127766271-7a3160a0-fdf6-4f33-a825-fcb7026a24d7.png)


The circuit can be represented by Mosfets as shown below. 


![image](https://user-images.githubusercontent.com/54993262/127766619-b1c9bdcb-e4e3-40df-b53f-85c203ab682d.png)

The disadvantage here is when both up and down transistors are off, there we can see a leakage current. Thus this current keeps charging the capacitor. 

# **_Loop Filter_**

<p align="center">
<img src="https://user-images.githubusercontent.com/54993262/127766789-a8cbabfc-2515-4f84-be2f-49ff45ae1dfd.png" width="300" height="400">
</p>



Adding a LPF not only smoothens the output but also stabilizes the PLL. Without LPF, PLL cannot lock and mimic the ref signal. We should set Cx to be a tenth of the capacitor. The loop filter bandwidth must be less than one tenth the highest output frequency. 

# **_Voltage Controlled Oscillator_**
The most comman one is the Ring oscillator. It contains odd number of inverters and flips the output. Since the flip is half of a period, the output will have a period twice the total delay of the series of inverters. We can achive control over the output by using current starving mechanism. 


![image](https://user-images.githubusercontent.com/54993262/127766948-2060fe31-8a03-4401-9f87-ae03f801f497.png)

*** 

**Lock Range** - The frequency range the PLL is able to follow input frequency variations once locked.

**Capture Range** - The frequency range the PLL is able to lock in when starting from an unlocked condition.

**Settling time** - The time within which the PLL is able to lock in from an unlocked condition. 

# **_PLL Components Circuit Design_**


## _Lab Setup and using Tools_
We mainly use Ngspice and Magic. Ngspice for transistor level simulation and Magic for layout design and parasitic extraction.

Ngspic: _ngspice<circuit_file_name>_

It plots the results based upon the simulation instruction given in the circuit file after simulating the given circuit file.

Magic: _magic - T <Technology_file_from_PDK><the_layout_file_to_open>_

It opens the layout file, where can view the layout and make modifications to it. Magic has many features, out of which we will be using _parasitic extraction_ and _GDS_ features.

We can simulate the **Frequency Divider** using ngspice. The circuit diagram is: 
![FreqDiv_Ckt](https://user-images.githubusercontent.com/54993262/127768760-8cab0492-3c56-4f08-adc7-edef3cea7d28.JPG)

``` touch FreqDiv.cir ```
![image](https://user-images.githubusercontent.com/54993262/127768546-a22a1430-ab7d-414a-a743-f086a7db3e57.png)
As seen above, V1 is given 1.8v and V2 is the pulse source, the input to frequency divider.
The _tran_ instruction tells the simulator to do a transient analysis by the given sampling rate

![FD1](https://user-images.githubusercontent.com/54993262/127768711-8b68767d-46f2-42aa-afd1-6b14467f31e8.JPG)
![FD2](https://user-images.githubusercontent.com/54993262/127768715-93a67fea-0028-45fb-802e-476db9e0f0da.JPG)

The simlation results are as follows:

![Freq_Div_OP](https://user-images.githubusercontent.com/54993262/127768748-ea40943a-87af-4e0f-a332-ec6beb43c484.JPG)

For **Charge Pump** it is as follows:
![image](https://user-images.githubusercontent.com/54993262/127768838-556eaa91-cd80-4893-8543-ed3c9f5c3a09.png)
_.ic_ indicates initial conditions of the signal 

![CP](https://user-images.githubusercontent.com/54993262/127768916-ce75ed7c-1efa-4b47-8cd1-35088a321084.JPG)
![CP_OP](https://user-images.githubusercontent.com/54993262/127768920-b40446e6-9891-4ddf-a151-9298c9fdd93f.JPG)

**VCO** :
``` vim VCO.cir ```
![image](https://user-images.githubusercontent.com/54993262/127769030-a9b18a3d-a834-437e-a069-a2b16c1ba950.png)

![VCO](https://user-images.githubusercontent.com/54993262/127769069-a19e4749-bf9e-4c74-b59e-dbd1bd2c0c7c.JPG)
Output :
![VCO_OP](https://user-images.githubusercontent.com/54993262/127769072-639c97e1-9ecc-47a5-8326-22afa7a69879.JPG)

**Phase Frequency Detector**
``` vim PD.cir```
![image](https://user-images.githubusercontent.com/54993262/127769110-878b39cc-7554-450a-8017-333f57b4ad54.png)
We can observe V3 and V2 have a phase difference of 6ns

![PD](https://user-images.githubusercontent.com/54993262/127769142-cf6d78be-e579-4773-985f-9c414b200b2e.JPG)
![PD_OP](https://user-images.githubusercontent.com/54993262/127769145-30a8ffdf-d173-4322-9cb6-002a8b8503fe.JPG)

Now combining all circuits and thus implementing a PLL:
![PreLay1](https://user-images.githubusercontent.com/54993262/127769791-a3e4b9cb-b391-48dd-bc40-84c870a470e8.JPG)
![PreLay2](https://user-images.githubusercontent.com/54993262/127769799-4139f280-dc1a-43e2-b4d1-6df240b04645.JPG)
![Prelay3F](https://user-images.githubusercontent.com/54993262/127769795-b9d56e78-6ea4-4a5d-9d6e-9af3d8f636d4.JPG)

Output of PLL:
![PreLayOP](https://user-images.githubusercontent.com/54993262/127769862-2f167d97-6195-47dd-a862-c0894c6f0016.JPG)


_Here the red signal is the reference signal. Blue is Output Clock Divided by 8, Yellow is the Down Signal, Brown is Up Signal and Pink is Charge pump output._


![PreLayOP2](https://user-images.githubusercontent.com/54993262/127769981-7939daf2-1d01-4e48-96a7-0553f8785ae7.JPG)


## _Troubleshooting Steps_
* If output doesn't match or mimic the ref signal, should always try to debug individual circuits beore simulating the whole circuit. If signals are coming flat up or the simulation is crashing then check whether the connectivity is done properly or any issues like wrong net names or parameter value issues.

If the signals are coming as expected but mimicing of signal is not happening then verify the following:

* If the VCO is working within the required range or not.

* Whether the PFD is able to detect small phase differences or not.

* The response of charge pump. The speed of it. If there is too much fluctuations in charging or discharging, then capacitor sizing is the thing where we have to pay the attention to. Also, check if there is charge leakage. If the charge pump is charging when the input is zero, then there is charge leakage issue.

* If nothing works out, then should adjust the loop filter accordingly.

## _Magic Layout_
In layout, different colour represents different components:

_n-well - slash lines_

_metal1 layer - purple_

_local interconnect layer - blue_

_p-diffusion - orange colour_

_n-diffusion - green colour_

_polysilicon - red_

**To view the layout of PFD using magic** ``` magic -T sky130A.tech PFD.mag         ```

![image](https://user-images.githubusercontent.com/54993262/127809855-14eda461-61df-400a-8674-c50c6c5122a0.png)

For Charge pump:

![image](https://user-images.githubusercontent.com/54993262/127809893-d32d6dcd-216a-4eb1-9e85-31ae1c0aae01.png)

VCO:

![image](https://user-images.githubusercontent.com/54993262/127809961-8287c74d-13cb-46a5-85f8-e53531627b80.png)


Frequency Divider:

![image](https://user-images.githubusercontent.com/54993262/127810026-d0829295-696e-424c-915e-948f2b18f63b.png)

_PLL when viewed in magic is as follows:_

![image](https://user-images.githubusercontent.com/54993262/127810137-f28ae502-0dab-45a0-9813-f26033e4c9e3.png)


## Tapeout
Tapeout is the final result of the design process for integrated circuits or printed circuit boards before they are sent for manufacturing. IO ports connect the silicon wafer to the outside world. We would also need peripherals such as UART or I2C. Memory and Testing mechanisms too are required. 

![image](https://user-images.githubusercontent.com/54993262/127813313-9ed5bc53-663f-45d6-87b9-02c30c317701.png)


![image](https://user-images.githubusercontent.com/54993262/127813741-20f6b9a3-8472-4df8-8963-f3008ba5ea8b.png)

We can zoom in to see the pins. Thus our PLL layout can be placed on this.


# References
* [Sky130 Technology](https://github.com/google/skywater-pdk)
