## Interlocalisation - Calibration
The interlocalisation system is very sensitive to any hardware changes. For this reason, the BeeBoard assembly should be calibrated in order to know exactly the system behavior and to correctly map the different measured parameters and the output angle, **this, for one precise assembly**.

#### Objective
As explained in [how the angle is calculated](how_it_works/angle.md), what is called the **angle** cannot be directly obtained from the values extracted from the DW1000 chips.
In fact, there is no one-to-one correspondence as there are many angle possiblities. To allow this correspondence a transformation has to be made on the angle measured from the PDOA values. The transformation is made from the following graph :
![](how_it_works/img/real_data_offset.png)

These values are obtained from the [extractRawData](https://github.com/SwarmUS/TestBench-Python#extractRawData) and [Parser](https://github.com/SwarmUS/TestBench-Python#parser) scripts using the test-bench *TurningStation*.

!!!note
	Please refer to these scripts [official documentation](https://github.com/SwarmUS/TestBench-Python#testbench-python) for a step-by-step description on the procedure to follow to acquire this kind of data.
	
Note that the theoretical values should follow the following trends :
![](how_it_works/img/theoretical_multi_angle.png)

As there is a considerable difference between the real and theoretical values, the real data has to be characterized, theoretical values will not give a proper rendition of the reality.
This characterization will permit the interlocalisation algortihm to compute actual angles from PDOA values. The theoretical values show a correspondence made of two linear slopes, one falling and one rising which repete themselves at each full turn. The calibration procedure will permit the extraction of these slopes for each antenna pair.

#### Procedure
The complete procedure on how to perform a calibration and how to validate the results is found [here](https://github.com/SwarmUS/TestBench-Python#testbench-python). Follow the flow chart to execute each scripts in the right order.

After the final validation step is successful, the assembly is ready to be mounted on its final destination, and the interlocalisation system should be operationnal.

!!!Caution
	The **assembly** is described as :<br />
	- The BeeBoard mount<br />
	- The 3 wings boards<br />
	- The 3 USC-C cables<br />
	- The HiveBoard<br />
	- The channel used for each BeeBoard.<br />

	If any of these is changed, or connected differently from the original calibration, the calibration must be re-done.<br />
	**Do not forget to re-write the parameters in the flash using [**Send_angle_params**](https://github.com/SwarmUS/TestBench-Python#send_angle_params)**