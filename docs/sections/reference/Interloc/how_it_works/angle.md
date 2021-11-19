# Angle Measurement

## Theory

Before explaining how angle measurement is done in the HiveMind, lets take a few steps back and see some of the theoretical fundametals (in a 2-antenna scenario) used in the calculation.

Because antennas in the system are so close to each other (2.7 cm), it is not possible to rely solely on timestamping messages received at different antennas to evaluate an angle. At 2.7 cm (the maximum time difference), radio waves only take 90 ps to travel. The DW1000 can timestamp with a 15 ps accuracy. When accounting for noise we can see that the time difference will be almost no use to estimate an angle with certainty.

The solution is to calculate a phase difference ($\alpha$) instead of a time difference. The antennas are placed at less then a wavelength (3.5 cm) appart. This way, the radio wave will hit each antenna at a different moment all while staying within the same period (because a period spans a distance of $\lambda/2$).

<figure markdown>
  ![Phase Difference](img/phase_diff.png){width="500"}
  <figcaption>Phase Difference</figcaption>
</figure>

Knowing the phase difference and doing a little trigonometry, the angle of arrival (AoA) can be calculated:
$$
\theta = arcsin\left(\frac{\alpha}{\pi}\right)
$$

Calculating $\alpha$ can be done by using registers found in the DW1000. The user is refered to the [DW1000 User Manual](https://www.decawave.com/sites/default/files/resources/dw1000_user_manual_2.11.pdf) and [an article](https://www.decawave.com/sites/default/files/angle_of_arrival_estimation_using_dw1000_online.pdf) published by DecaWave on the subject.

$$
\alpha = \left(\left( \phi_A - \beta_A - \phi_B + \beta_B + \pi \right) mod 2\pi \right) - \pi
$$

Where $\phi$ is the synchronization frame angle and $\beta$ is the first path angle. 

The $arcsin()$ in the equation allows a one-to-one linear mapping of phase differences from $-\pi/2$ to $\pi/2$ to angles going from $-\pi/2$ to $\pi/2$. To bring the mapping to angles between $0$ and $\pi$, an offset is added to the equation presented earlier:
$$
\alpha = \left(\left( \phi_A - \beta_A - \phi_B + \beta_B - \frac{\pi}{2} \right) mod 2\pi \right) - \pi
$$

The problem with this approach is that a one-to-one mapping is only available from $-\pi/2$ to $\pi/2$. For the second half of the circle, the same values come back only mirrored, making it impossible to determine what the actual angle is.
<figure markdown>
  ![Angle measurement with a single antenna pair](img/theoretical_single_angle.png)
  <figcaption>Angle measurement with a single antenna pair</figcaption>
</figure>


## Expanding Beyond 180°

To be able to measure the angle of arrival on the whole unit circle, three antennas must be used. By placing antennas in an equilateral triangle, we can calculate the AoA for each of the three antenna pairs which will all be offset by 60°

<figure markdown>
  ![Angle measurement with multiple antenna pairs](img/theoretical_multi_angle.png)
  <figcaption>Angle measurement with multiple antenna pairs</figcaption>
</figure>

The problem of knowing which mapping (the downwards or upwards slople) to use for each pair is still present. However, by using information from each of the three pairs, it is possible to know which slope to choose. By placing imaginary lines at +/- 30°, we can use the AoA value of another antenna pair to know which slope to use for the current pair.

!!!note
    Don't forget the x-axis is the **real** angle. So what we are measuring is a point on the y-axis to which we are trying to map a point on the x-axis.

<figure markdown>
  ![Choosing the correct slope](img/decision_line.png)
  <figcaption>Choosing the correct slope</figcaption>
</figure>

For example, take pair 1. The rising slope should be chosen when pair 2 **or** pair 3 is below the -30° line while the falling slope should be chosen when pair 2 **or** pair 3 is above the +30° line. At any given angle, only one curve can be measured between the two lines. This gives a way to know with certainty *(in theory)* which slope to use for all three of the pairs.

## Implementation & Certainty

## Further Reading