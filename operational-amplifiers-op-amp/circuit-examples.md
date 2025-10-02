# Circuit Examples

## Operational Amplifier Basics with 6 Circuit Examples

by [Ankit Negi](https://www.etechnophiles.com/author/ankit-negi/)![Operational Amplifier basics with 6 Circuit Examples](https://www.etechnophiles.com/wp-content/uploads/2020/03/Operational-Amplifier-basics-with-6-Circuit-Examples-300x166.webp)

Last updated on March 27th, 2024 at 05:45 pm

Operational amplifiers, commonly known as opamps are the most common type of building block in analog electronics. Opamps are used to make power amplifiers, sensitive preamplifiers, logarithmic amplifiers, RC oscillators that generate sine, triangle, and square waveforms, LC oscillators, high slope filters, and a whole lot more.

<figure><img src="https://www.etechnophiles.com/wp-content/uploads/2020/03/opamp_article_html_d545185da6e0490.png" alt="OPAMP SYMBOL" height="192" width="204"><figcaption><p>OPAMP SYMBOL</p></figcaption></figure>



An op-amp has two inputs, an inverting terminal (labeled „-”) and a non-inverting terminal (labeled „+”). And has a single output. The first input is called inverting because the output voltage is inverse of the voltage applied at the **inverting input, times the gain of the amplifier circuit**. If we apply the signal to the non-inverting input we get the same signal on the output, times gain.

### Table of Contents

* [What is Negative feedback in an opamp?](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#what-is-negative-feedback-in-an-opamp)
* [Important Op-amp parameters](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#important-op-amp-parameters)
* [Main configurations of op-amp](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#main-configurations-of-op-amp)
  * [Open-loop amplifier](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#open-loop-amplifier)
  * [Non-inverting](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#non-inverting)
  * [Inverting amplifier](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#inverting-amplifier)
* [Difference between Inverting and Non-Inverting amplifier](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#difference-between-inverting-and-non-inverting-amplifier)
* [Op-amp in single supply operation](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#op-amp-in-single-supply-operation)
* [Projects and circuit examples of operational amplifier](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#projects-and-circuit-examples-of-operational-amplifier)
  * [1. DC Non-inverting amplifier](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#1-dc-non-inverting-amplifier)
  * [2. Inverting audio preamplifier circuit](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#2-inverting-audio-preamplifier-circuit)
  * [3. Electret microphone preamplifier circuit ](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#3-electret-microphone-preamplifier-circuit)
  * [4. Class-AB audio power amplifier circuit](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#4-class-ab-audio-power-amplifier-circuit)
  * [5. 3000Hz Active Low-pass filter for radio communications](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#5-3000-hz-active-low-pass-filter-for-radio-communications)
  * [6. Relaxation oscillator](https://www.etechnophiles.com/operational-amplifier-basics-with-6-circuit-examples/#6-relaxation-oscillator)

### What is Negative feedback in an opamp? <a href="#what-is-negative-feedback-in-an-opamp" id="what-is-negative-feedback-in-an-opamp"></a>

Most opamp circuits use negative feedback to limit the ideal infinite gain of an opamp to the desired value.&#x20;

**In Negative feedback, the output signal, which is 180° out of phase about the input is fed back to the same input, usually by some divider network.**

This feedback voltage from the output which is always of inverse polarity than the input voltage, “pulls” the actual input down and makes the overall input voltage less than the voltage that was applied at the input.



This feedback allows for great control of opamp gain so that the gain of a circuit utilizing negative feedback is not determined by the gain of the device used (opamp or transistor) but by the feedback itself (as long as the gain defined by feedback is considerably lower than the gain of the device used).

### Important Op-amp parameters <a href="#important-op-amp-parameters" id="important-op-amp-parameters"></a>

An ideal opamp has infinite gain without feedback (open-loop), zero noise, infinite input resistance, zero output resistance, infinite slew rate, and infinite bandwidth.

Common opamps, such as the fabled LM741 or LM358,  LM324 (LM358 in a quad package), and BA4558 have an **open-loop gain** of around 100 000, **unity-gain bandwidth** of around 1MHz, and **input resistances** of around 1MΩ.

**Noise parameters** vary a lot from opamp to opamp. Typical equivalent input noise parameters, with a bandwidth of 20kHz, as in audio circuits, (noise voltage depends on bandwidth, the higher the bandwidth, the higher the noise) are below 7uV (50nV/√Hz), LM741 has 2.9uV (20nV/√Hz), BA4558 has 1.7uV (12nV/√Hz) and even as low as 0.64uV (4.5nV/√Hz) for μPC4570C.

Noise parameters can be measured either in uV in the desired bandwidth or in nV/√Hz, which is nanovolts of noise on the input divided by the square root of the bandwidth.

**The slew** rate is how fast the opamps can swing their outputs. It is measured in V/μs or how fast can the output voltage increase in one microsecond. LM358 has a slew rate of around 0.55V/μs.

### Main configurations of op-amp <a href="#main-configurations-of-op-amp" id="main-configurations-of-op-amp"></a>

There are 3 main amplifier configurations of opamps with negative feedback:

* Open-loop amplifier (Comparator/Differentiator)
* Non-inverting amplifier. Unity gain buffer (Voltage Follower)
* Inverting amplifier

#### Open-loop amplifier <a href="#open-loop-amplifier" id="open-loop-amplifier"></a>

This type of amplifier is a special one because no negative feedback is used to limit the gain. The signal can be applied at either input, but the other input has to be grounded. If the signal is weak, let’s say 10μV, and our opamp has an open-loop gain of 100 000, the output signal will be 1V.



This much gain is seldom needed on its own, it also provides an opportunity for parasitic oscillations to occur. If the open-loop gain parameter is not tightly controlled during manufacturing, opamps of the same type can give different open-loop gains. The open-loop amplifier can also be used as an analog comparator. In fact, comparators are opamps with different names.

#### Non-inverting <a href="#non-inverting" id="non-inverting"></a>

The non-inverting amplifier uses negative feedback to reduce the gain to a required value. This way the gain of the circuit is not governed by the open-loop gain of the opamp, but by a set of feedback resistors, allowing for more flexibility.

The input signal is fed straight to the positive input of the amplifier, causing the input impedance to be practically equal to the input impedance of the opamp at audio frequencies.



The negative feedback and therefore gain (Av) is set by the ratio of resistors R1 and R2 and is always greater or equal to one.

<figure><img src="https://www.etechnophiles.com/wp-content/uploads/2020/03/opamp_article_html_6abe64123294969f.png" alt="Gain formula for Non-Inverting amplifier" height="120" width="222"><figcaption><p><em>The gain formula for a Non-Inverting amplifier</em></p></figcaption></figure>

**Voltage follower or Unity gain buffer**



A special case of the non-inverting amplifier is the unity gain buffer, where instead of the feedback network the negative input is connected directly to the output. This causes the voltage gain to be unity (equal to one, A<sub>v</sub>=1).

This configuration is used in active audio filters, opamp headphone amplifiers, and wherever there is a need for a high input impedance buffer stage. This circuit can be compared to a common-collector transistor amplifier configuration.

#### Inverting amplifier <a href="#inverting-amplifier" id="inverting-amplifier"></a>

An Inverting amplifier differs from a non-inverting amplifier due to a much lower input impedance (equal to the value of R1). The output signal in an inverting amplifier is inverted concerning the input signal. If a 1V DC signal is fed to an inverting amplifier with a gain of 10 we get a -10V DC signal on the output. For AC signals the process is similar, but we can say that the signal has shifted by 180°, like in a common-emitter amplifier.



The negative feedback and therefore gain (Av) is set by the ratio of resistors R2 and R1. The inverting configuration allows for gains both higher and lower than one.

<figure><img src="https://www.etechnophiles.com/wp-content/uploads/2020/03/opamp_article_html_7583d17f7a5b5eb9.png" alt="Gain formula for Inverting amplifier" height="113" width="154"><figcaption><p><em>The gain formula for Inverting amplifier</em></p></figcaption></figure>

Similar to the other opamp circuits the voltages on both the inputs turn out to be the same(due to the opamp properties). Hence if the positive input is grounded, the negative input will also be grounded or at 0 volts. Now the feedback voltage from the output combines with the input voltage and since these voltages are of opposite polarities the resulting voltage is zero volts, explaining the low input impedance.

### Difference between Inverting and Non-Inverting amplifier <a href="#difference-between-inverting-and-non-inverting-amplifier" id="difference-between-inverting-and-non-inverting-amplifier"></a>

<figure><img src="https://www.etechnophiles.com/wp-content/uploads/2020/03/inverting-and-Non-inverting-amplifier-1024x467.png" alt="Difference between Inverting &#x26; Non-inverting amplifier" height="467" width="1024"><figcaption><p>  <em>Difference between Inverting &#x26; Non-inverting amplifier</em></p></figcaption></figure>

Overall both the inverting and non-inverting amplifiers can provide good performance, the only difference is of input impedance. The low input impedance of the inverting amplifier is useful where a set input impedance is needed, for example in systems that use transmission lines that have a set impedance or LC filters.

The non-inverting amplifier is useful where a high input impedance is necessary, for example in stages following active filters, oscillators, audio amplifiers, DC amplifiers used in voltmeters, etc.

Another advantage of the inverting amplifier is that the gain can be lower than one, unlike the non-inverting amplifier with its gain being always higher than one.

### Op-amp in single supply operation <a href="#op-amp-in-single-supply-operation" id="op-amp-in-single-supply-operation"></a>

All the circuits provided above only show the feedback resistors. One might be tempted to think that this is all you need for an opamp to work with a single supply, like using one 9V battery or 5V from the USB.

<figure><img src="https://www.etechnophiles.com/wp-content/uploads/2020/03/image-1024x445.png" alt="Opamp single supply operation" height="445" width="1024"><figcaption><p>           <em>Opamp single-supply operation</em></p></figcaption></figure>

This will not work, as the positive (+) and negative (-) inputs are not biased in any way. Opamps have to be biased just like transistors when they use a single supply instead of a dual (also known as bipolar) positive and negative supply (that is why the LM741 has V+ and V-, not just V+ and GND).

To bias them properly you need to connect a 100kΩ resistor to the supply and another 100kΩ to the ground (If you are using a FET or a high input impedance opamp you can use two 1MΩ resistors). If a resistor is connected from the output to the input it will both bias the input, since the DC voltage at the output of an opamp is around one-half of the supply voltage (4.5V for 9V supply) and that voltage biases the amplifier.

**A special case is the LM324, which is a single-supply opamp, meaning the inputs are already biased and don’t need any external resistors.**

But external capacitors are required in AC circuits to prevent the DC bias voltage. This DC bias voltage can be present:

* **On the inputs and outputs or where they’re not supposed to be.**
* **Where external feedback resistors might affect biasing.**

### **Projects and circuit examples of operational amplifier** <a href="#projects-and-circuit-examples-of-operational-amplifier" id="projects-and-circuit-examples-of-operational-amplifier"></a>

#### 1. DC Non-inverting amplifier <a href="#id-1-dc-non-inverting-amplifier" id="id-1-dc-non-inverting-amplifier"></a>

**Objective**: This circuit can be used to improve the selectivity of your multimeter for measuring small DC voltages.

**Circuit diagram**



**Components required**

| **RESISTORS**                                |  **OPAMP IC**                                                              |
| -------------------------------------------- | -------------------------------------------------------------------------- |
| <p>R1 – 1k</p><p>R2 – 10k</p><p>R3 – 10k</p> | U – LM741, TL081, TL071, or any opamp meant for DC amplifier applications. |

**Working of DC Non-Inverting amplifier**&#x20;

This circuit uses an LM741 BJT opamp, but using the TL081 and 2.2MΩ can improve the input impedance from around 100kΩ to 1MΩ. It has an adjustable gain that can be set to 10, for easier reading of the output voltage (1mV gives 10mV instead of 11mV for a gain of 11 with 10k and 100k resistors).

R1 is the offset null control – it is a trimmer that has to be set to a value, so the voltage on both the inverting and non-inverting input is to be equal.

R2 and R3 set the gain, and it should be set to 10, so reading the voltage is easier. It can be easily supplied with two 9V batteries, thus making it portable.

#### 2. **Inverting audio preamplifier circuit** <a href="#id-2-inverting-audio-preamplifier-circuit" id="id-2-inverting-audio-preamplifier-circuit"></a>

Objective: This circuit can act as an audio preamplifier, either on its own or as a part of a larger audio amplifier.

**Circuit diagram**



**Components required**

| **RESISTOR**                                                    | **CAPACITOR**                                                                                          | **Opamp IC**                                                                                                    |
| --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| <p>R1 – 100k</p><p>R2 – 100k</p><p>R3 – 10k</p><p>R4 – 100k</p> | <p>C1 – 100nF</p><p>C2 – 10uF</p><p>C3 – 100nF</p><p>C4 – 470uF when using phones, 100uF otherwise</p> | U – LM741, TL081, TL071, LM358, BA4558 or any other common opamp, even a power amplifier like TDA2030 can work. |

**Working of Inverting audio pre-amplifier**&#x20;

This circuit has an audio gain of 10 and an input impedance of 10kΩ. It can be used as an audio preamplifier, either on its own or as a part of a larger audio amplifier. It can also be used to drive a pair of 32Ω headphones at 5mW or a pair of high impedance 200Ω headphones at 40mW when supplied with 9V and steered with a strong enough signal. This low output power is due to the maximum output current of the LM741 being 25mA, typical of most opamps. Higher power opamps will give much higher output powers.

R1 and R2 bias the positive input (most opamps can’t work in a single supply setting without bias), C1 grounds the positive input for AC signals (in the inverting configuration the possible input has to be grounded for the signal).

R3 and R4 provide negative feedback, limiting gain to 10, apart from feedback R4 provide bias to the negative input and R3 sets the input impedance of the amplifier.

C3 decouples the power supply from noise and ripples, it should be placed as close to the amplifier chip as possible. C4 only lets the AC signal to flow through it, stopping any DC voltage on the output of the opamp from flowing into the speaker.

#### 3. Electret microphone preamplifier circuit  <a href="#id-3-electret-microphone-preamplifier-circuit" id="id-3-electret-microphone-preamplifier-circuit"></a>

**Objective**: This circuit can be used to amplify the very weak signal (<10mV) of an electret microphone before going to the speaker output.

**Circuit diagram**



**Components required**

| **RESISTOR**                                                                               | **CAPACITOR**                                                                                                                      |   **Opamp IC**                                                             |
| ------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| <p>R1 – 10k</p><p>R2 – 220kΩ</p><p>R3 – 220kΩ</p><p>R4 – 100kΩ trimmer</p><p>R5 – 2.2k</p> | <p>C1 – 100nF</p><p>C2 – 4.7uF</p><p>C3 – 100nF</p><p>C4 – 100uF</p><p>C5 – 470uF for headphones, 1000uF for 8Ω, 2200uF for 4Ω</p> | U – BA4558, RC4558, LM741, TL081, TL071, LM358, or any other common opamp. |

**Working of Electret Microphone Preamplifier Circuit:**&#x20;

The voltage gain of this circuit is adjusted using R4 from about 45 down to 1. You can substitute R4 with a set value resistor if you know the required gain, but it has to be smaller than 220kΩ.

R1 biases the electret microphone (M, due to the nature of electret microphones they have to be supplied with power, as there is a JFET inside of them).

C1 prevents the DC bias voltage from being affected by the low resistance of the microphone, while R2 and R3 bias the positive input of the opamp. C3 filters and decouples the voltage supply and prevents parasitic oscillation, R4 provides the bias to the negative input, while R4 and R5 together set negative feedback and therefore gain.

C2 blocks DC bias from being affected by R5, as its low resistance would lower the negative input bias from half of the supply voltage to fractions of a volt. C4 blocks the DC voltage from the output of the amplifier and only lets the amplified AC microphone signal through.

#### 4. Class-AB audio power amplifier circuit <a href="#id-4-class-ab-audio-power-amplifier-circuit" id="id-4-class-ab-audio-power-amplifier-circuit"></a>

**Objective**: This simple circuit is a complete audio amplifier that can give some serious output power.

**Circuit diagram**



**Components required**

| **RESISTOR**                                                                                       | **CAPACITOR**                                                                                                                     | **Opamp IC**                                                                                                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| <p>R1 – 47kΩ logarithmic (B)</p><p>R2 – 220kΩ</p><p>R3 – 220kΩ</p><p>R4 – 100kΩ</p><p>R5 – 470</p> | <p>C1 – 100nF</p><p>C2 – 47uF</p><p>C3– 1000uF</p><p>C4 – 100nF</p><p>C5 – 470uF for headphones, 1000uF for 8Ω, 2200uF for 4Ω</p> | <p> </p><p>Q1, Q2 – Both transistors matched (roughly the same hFE)</p><p>U(IC) – Best TL082/TL072 and other high slew rate opamps for lowest distortion, BA4558 or any 4558 opamps will work too, LM358 will work but with much worse high-frequency performance (distortion above 5kHz). All have the same pinout.</p> |

**Working of class AB Audio Power Amplifier circuit:** &#x20;

This circuit uses a double opamp, the first section is the preamplifier with a gain of around 200, the second one is used as a unity-gain driver that steers the power transistors Q1 and Q2.

The very strong negative feedback makes sure the audio is not distorted.

R1 is the volume control, and R2 and R3 bias the positive input of the first opamp. R4 and R5 set preamplifier gain with R4 also biasing the negative input, C2 blocks DC, otherwise, the DC bias voltage will be reduced by R4 and R5 acting like a voltage divider and the amplifier will not work. C3 and C4 decouple the supply from noise and 50Hz hum. C5 blocks DC and only lets through the amplified AC audio signal to the speaker.

Used transistors depend on the required output power:

* [2N3904](https://www.etechnophiles.com/2n3904-transistor-pinout-datasheet-specs/) and 2N3906 for 50mW at 4Ω and 100mW at 8Ω (5V-9V supply),
* BD139 and BD140 for 4W at 4Ω and 7W at 8Ω (12V)
* [TIP120](https://www.etechnophiles.com/tip120-transistor-pinout-datasheet-arduino-circuit/), TIP125 up to 20W at 4Ω and 12W at 8Ω (12V, more at 24V)

#### 5. 3000Hz Active Low-pass filter for radio communications <a href="#id-5-3000-hz-active-low-pass-filter-for-radio-communications" id="id-5-3000-hz-active-low-pass-filter-for-radio-communications"></a>

**Objective**: This circuit acts as a 3000Hz low-pass filter and an amplifier.

**Circuit diagram**



**Components required**

| **RESISTOR**                                                                                        | **CAPACITORS**                                                                      |  **Opamp IC**                                                                        |
| --------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| <p>R1 – 22kΩ</p><p>R2 – 10kΩ</p><p>R3 – 100kΩ</p><p>R4 – 100kΩ</p><p>R5 – 47kΩ</p><p>R6 – 4.7kΩ</p> | <p>C1 – 2.2nF</p><p>C2 – 100nF</p><p>C3– 10nF</p><p>C4 – 100nF</p><p>C5 – 100nF</p> | Opamp Ic(U) – BA4558, RC4558, TL082, TL072, LM358, or any other common double opamp. |

**Working of 3000Hz active low pass filter amplifier**

The first opamp forms a Sallen-Key active filter with much greater performance than a simple RC filter (5dB/1kHz vs 1.6dB/kHz roll-off above the max. frequency). The second opamp provides a gain of around 11 and can be used to drive a pair of headphones (impedance higher than 10 ohms) or another audio amplifier stage. An amplifier is required after an active filter stage because if the filter is loaded with a low impedance load then the filter’s performance will be significantly degraded.

R1, R2, C1, and C3-  sets negative feedback, the cutoff frequency and the Q-factor (how sharp the filter is) of the filter. C2 prevents any DC voltage from being present on the input.

R3 and R4 bias the positive input of the filter, the negative input is biased from the output of the filter (the output has half of the supply voltage on it, ideal for biasing the input).

R5 and R6 provide negative feedback and set the gain (), C4 blocks the flow of DC through R6 that would change the negative input bias. The positive input doesn’t have any biasing resistors, because it is biased by the output of the first opamp (the output of the opamp normally has half of the supply voltage on it, just what we need for the bias of the input).

C5 decouples the power supply and prevents parasitic oscillation whereas C6 lets the filtered amplified input signal to the output while preventing any DC offset at the same time.

#### 6. Relaxation oscillator <a href="#id-6-relaxation-oscillator" id="id-6-relaxation-oscillator"></a>

**Objective**: To build a relaxation oscillator circuit using LM741. The relaxation oscillator is a very simple oscillator circuit that gives a high output amplitude with a square waveform.

**Circuit diagram**



**Components required**

| **RESISTORS**                                                            | **CAPACITORS**                                                                                                                    | **Opamp IC**                                                                                                                                                         |
| ------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>R1 – 22kΩ</p><p>R2 – 22kΩ</p><p>R3 – 47kΩ logarithmic (B)</p><p> </p> | <p>C1 – 10uF</p><p>C2 – 100nF</p><p>C3 – 100nF</p><p>C4 – depends on the required frequency</p><p>C5 – 100uF</p><p>C6 – 100nF</p> | Opamp IC(U) – LM741, TL081, TL071, LM358, BA4558 or any other common opamp. High-speed and high-frequency types such as TL081, and TL071 are preferable over 100kHz. |

**Working of Relaxation oscillator circuit:**&#x20;

The relaxation oscillator is a very simple oscillator circuit that gives a high output amplitude with a square waveform. With a 47kΩ [potentiometer](https://www.etechnophiles.com/what-is-a-potentiometer/), it can work from fractions of a Hz to hundreds of kHz, with only values of C4 changing, depending on the desired frequency range. The frequency of oscillation is determined by this formula: With f in hertz, R in ohms and C in farads.

<figure><img src="https://www.etechnophiles.com/wp-content/uploads/2020/03/opamp_article_html_856ddea45456e0a5-300x162.png" alt="Opamp Osillator frequency formula" height="162" width="300"><figcaption><p> <em>Opamp Oscillator frequency formula</em></p></figcaption></figure>

Different from all other circuits, oscillators use positive feedback, here it is applied from the output to the positive input, similar to how negative feedback is used in non-inverting amplifiers.

R1 and R2 provide positive feedback, C2 and C1 prevent DC from flowing R1 to ground and also prevents R1 and R2 to act as a voltage divider for the positive input – this would cause the input to be under biased (instead of getting half of the supply voltage it needs, it would get ¼, because R1 and R2 divide the voltage by one half and we already have half of the supply on the outputs) and the opamp might not work correctly.

There is a 100nF capacitor (C2) in parallel with C1 because electrolytic capacitors have bad performance above 20kHz – This prevents distortion of the square wave at high frequencies. C3 decouples the power supply from interference caused by the oscillator and prevents high frequency “ringing” on the output square wave and parasitic oscillations on frequencies different than the one we want to generate.

C4 and R3 determine the frequency of oscillation, with R3 also biasing the negative input of the opamp. C5 and C6 pass the generated signal while stopping the DC voltage present on the output. Just like capacitors C1 and C2, using a 100nF capacitor in parallel with an electrolytic capacitor improves frequency response. This oscillator does not give an ideal square wave with a perfect 50% duty cycle – if a perfect 50% duty cycle is needed R2 is to be replaced with a 22k or 10k potentiometer/trimmer.

***

**Sources**

Douglas Self – “Electronics for Vinyl”

Stan Gibilisco and Simon Monk – “Teach Yourself Electricity and Electronics, Sixth Edition”,  McGraw-Hill Education, 2016, ISBN 978-1-25-958553-1

Multiple Authors – “Poradnik Radioamatora, Wydanie drugie zmienione”, WKŁ, Warsaw 1983, ISBN 83-206-0307-2

[Electronics-notes.com](https://www.electronics-notes.com/articles/analogue_circuits/operational-amplifier-op-amp/non-inverting-amplifier.php)

[Wikipedia](https://en.wikipedia.org/wiki/Relaxation_oscillator)

[Texas-Instruments(1)](https://www.ti.com/lit/ds/symlink/lm158-n.pdf)

[Texas-Instruments(2)](https://www.ti.com/product/LM324)

[Sim.okawa-denshi](https://sim.okawa-denshi.jp/en/OPstool.php)

[CircuitDigest](https://circuitdigest.com/calculators/op-amp-gain-calculator)

[Electronics-Tutorials](https://www.electronics-tutorials.ws/opamp/opamp_2.html)
