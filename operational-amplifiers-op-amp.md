# Operational amplifiers (op amp)

As well as [resistors](https://www.arrow.com/categories/resistors) and [capacitors](https://www.arrow.com/categories/capacitors) that are passive components, operational amplifiers are one of the basic building blocks of analogue electronic circuits.

[Operational amplifiers (op amp)](https://www.arrow.com/en/categories/amplifiers/amplifiers-and-comparators/op-amps) are linear devices that have all the properties required for nearly ideal DC amplification and are therefore used extensively in signal conditioning or filtering or to perform mathematical operations such as adding, subtracting, integration, and differentiation. The purpose of this article is to present 10 basics circuits for newcomers to electronics designs and to refresh the rusty minds of engineers.

### 1. Voltage follower

The most basic circuit is the voltage buffer, as it does not require any external components. As the voltage output is equal to the voltage input, students might become puzzled and wonder whether this kind of circuit has any practical application.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-1.jpg?la=it-it\&h=292\&w=400\&hash=88ED136A71E88D4F9E3FA5D0E8BFBE59)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-2.jpg?la=it-it\&hash=748E645A9552D6BAD17424A9A8E1EB23)

This circuit allows for the creation of a very high impedance input and low impedance output. This is useful to interface logic levels between two components or when a power supply is based on a voltage divider. The figure below is based on a voltage divider, and the circuit cannot function. Indeed, the load impedance can have large variations, so V<sub>out</sub> voltage can change dramatically, mainly if the load impedance has a value of the same magnitude as R2.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-3.jpg?la=it-it\&hash=E4FCF4EFAC51FCE4F0A24923A8AEDDA2)

To solve this issue, an amplifier between the load and the voltage divider (see figure below) is inserted. Thus, V<sub>out</sub> depends on R1 and R2 and not on load value.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-4.jpg?la=it-it\&hash=EC16206A26163A23148C9C6794B4AE66)

The primary goal of an operational amplifier, as its names states, is to amplify a signal. For instance, the output of a sensor must be amplified in order to have the ADC measure this signal.

### 2. Inverting op amp

In this configuration, the output is fed back to the negative or inverting input through a resistor (R2). The input signal is applied to this inverting pin through a resistor (R1).

The positive pin is connected to ground.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-5.jpg?la=it-it\&h=239\&w=400\&hash=1846D5A383330663DC8494C78EF2A336)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-6.jpg?la=it-it\&hash=01BC58B5C1028B3F112F7F6441C92350)

This is evident in the special case where R1 and R2 are equal. This configuration allows for the production of a signal that is complementary to the input, as the output is exactly the opposite of the input signal.

Due to the negative sign, the output and input signals are out of phase. If both signals must be in phase, a non-inverting amplifier is used.

### 3. Non-inverting op amp

This configuration is very similar to the inverting operation amplifier. For the non-inverting one, the input voltage is directly to the applied to the non-inverting pin and the end of feedback loop is connected to ground.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-7.jpg?la=it-it\&h=275\&w=400\&hash=36EF357CDF4DC3486037BB270E2A6609)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-8.jpg?la=it-it\&hash=F14DF8C5B8BA87DB49841C465F020E12)

These configurations allow amplification of one signal. It’s possible to amplify several signals by using summing amplifiers.

### 4. Non-inverting summing amplifier

To add 2 voltages, only 2 resistors can be added on the positive pin to the non-inverting operational amplifier circuit.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-9.jpg?la=it-it\&h=268\&w=400\&hash=9AF79F2E1171B7BB6D158B8C17D4EE6C)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-10.jpg?la=it-it\&hash=7A9D55B0B24712DB0EB67838543B6C74)

It is worth noticing that adding several voltages is not a very flexible solution. Indeed, if a third voltage is added with exactly the same resistances, the formula would be Vs = 2/3 (V<sub>1</sub> + V<sub>2</sub> + V<sub>3</sub>).

The resistors would need to be changed to get Vs = V<sub>1</sub> + V<sub>2</sub> + V<sub>3,</sub> or a second option is to use an inverting summer amplifier.

### 5. Inverting summing amplifier

By adding resistors in parallel on the inverting input pin of the inverting operation amplifier circuit, all the voltages are summed.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-11.jpg?la=it-it\&h=318\&w=400\&hash=FA2E138822CDD351AA6196B74A055315)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-12.jpg?la=it-it\&hash=EF37A2478C8B661A022EDD5CC04623E2)

Unlike the non-inverting summing amplifier, any number of voltages can be added without changing resistor values.

### 6. Differential amplifier

The inverting operational amplifier (see circuit number 2) amplified a voltage that was applied on the inverting pin, and the output voltage was out of phase. The non-inverting pin is connected to ground with this configuration.

If the above circuit is modified by applying a voltage through a voltage divider on the non-inverting, we end up with a differential amplifier as shown below.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-13.jpg?la=it-it\&h=316\&w=400\&hash=FC0CE6876547A56A618BE501A10B996F)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-14.jpg?la=it-it\&hash=8CD9F1A4104E2977D6C20009DB2745D9)

An amplifier is useful not just because it lets you add, subtract, or compare voltages. Many circuits allow you to modify signals. Let’s see the most basic ones.

### 7. Integrator

A square wave is very easy to generate, by just toggling a GPIO of a microcontroller for example. If a circuit needs a triangle waveform, a good way to do it is just integrating the square wave signal. With an operation amplifier, a capacitor on the inverting feedback path, and a resistor on the input inverting pin as shown below, the input signal is integrated.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-15.jpg?la=it-it\&h=255\&w=400\&hash=69AF0DD8B034B5445F72F41D502034C3)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-16.jpg?la=it-it\&hash=36A9D476E75A6E57338CABFCD24B9C38)

Be aware that a resistor is often connected in parallel to the capacitor for saturation issues. Indeed, if the input signal is a very low frequency sine wave, the capacitor acts like an open circuit and blocks feedback voltage. The amplifier is then like a normal open-loop amplifier that has very high open-loop gain, and the amplifier is saturated. Thanks to a resistor in parallel of the capacitor, the circuit behaves like an inverting amplifier with a low frequency, and saturation is avoided.

### 8. Op amp differentiator

The differentiator works similarly to the integrator by swapping the capacitor and the resistor.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-17.jpg?la=it-it\&h=266\&w=400\&hash=1E894C6580897668C0F3EF6C719D29B5)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-18.jpg?la=it-it\&hash=381DD41EF2E3628ABCA6ADD7D60D055E)

All the configurations that were presented up to now.

### 9. Converter current – voltage

A [photodetector](https://www.arrow.com/en/categories/optoelectronics/photoelement/photodetectors) converts light into current. To convert the current into voltage, a simple circuit with an operational amplifier, a feedback loop through a resistor on the non-inverting, and the diode connected between the two input pins allows you to get an output voltage proportional to current generated by the photodiode, which is evident by the light characteristics.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-19.jpg?la=it-it\&h=275\&w=400\&hash=E2C2DE3F9C3E298D4B76F0EC481797EC)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-20.jpg?la=it-it\&hash=84B6AB8AA7BFEC218CE918CEF5B29AB1)

The above circuit applies Ohm’s law with the fundamental formula: voltage is equal to resistance multiplied by current. The resistance is in Ohms and is always positive. But thanks to operational amplifiers, a negative resistance can be designed!

### 10. Negative resistance

A feedback on the inverting pin forces the output voltage to be the double of the input voltage. As the output voltage is always higher than the input voltage, the positive feedback through the R1 resistor on the non-inverting pin simulates a negative resistance.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-21.jpg?la=it-it\&h=327\&w=400\&hash=D85D53A86109EB11898C6ED0DDFD03BB)

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-22.jpg?la=it-it\&hash=BA443AB43FDC92B573922FA4123BB691)

Finally, a circuit with operational amplifier does not necessarily modify the input signal, but records it like the peak detector amplifier.

### Also: Peak detector operation amplifier

The capacitor is used as a memory. When the input voltage on the non-inverting input is higher than the voltage on the inverting input that is also the voltage across the capacitor, the amplifier enters in saturation and the diode is forward and charges the capacitor. Assuming the capacitor does not have a quick self discharge, when the input voltage Ve is lower than voltage across the capacitor, the diode is blocked. Hence, the peak voltage is recorded thanks to the capacitor.

![](https://static4.arrow.com/-/media/arrow/images/miscellaneous/1/1116-op-amp-fun-image-23.jpg?la=it-it\&h=229\&w=400\&hash=2F6480A108931285AD186608AC9E58D0)

Many more circuits are available with operational amplifiers, but understanding these fundamental 10 circuits allows you to easily study more complex circuits.



<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
