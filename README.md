# Ultrasonic communications (STM32L4 DSP w/ MEMS mic)

![ChirpFrame](./doc/ChirpFrame.jpg)

As an IoT hobyyist, I am becoming interested in developing an IoT demo using DSP with MEMS mic.

This project is to study Arm Cortex-M4's DSP capabilities. STMicro gave me the STM32L4 and MEMS mic free at some trade show in Chiba, Japan. Thanks a lot to STMicro!

I am a beginner in the area of digital signaling processing. I will study mathematics required for DSP by developing a prototype on DSP this time.

## Preparation: STM32L4 platform and FFT test code on MEMS mic

This project uses STM32L476RG as MCU/DSP and MP34ST01-M as MEMS microphone:

![platform](./doc/MEMSMIC_expansion_board.jpg)

The system architecture is as follows:

![architecture](https://docs.google.com/drawings/d/e/2PACX-1vR1KKp2QeL_SmrnUsTl5zcwddQToPJmnSBHFnxiw78y3_3mjA7EzNl2iNcUA5aOW_jRAQapTNji-eJ7/pub?w=2268&h=567)

==> [Platform](PLATFORM.md)

==> [Test code](./basic)

## Ultrasonic communications experiment (FSK modulation)

My first idea was FSK modulation of 18 frequencies (18 symboles) to send data on ultrasonic wave. The method worked very well in a silent room, but did not work in a noisy environment such as a meeting room. I had to come up with another approach, such as spread spectrum.

![](./doc/18symbols.jpg)

==> [Experiment](EXPERIMENT.md)

==> [Test code](./ultracom)


## Ultrasonic communications experiment (Chirp modulation)

I tested Chirp modulation as one of spread spectrum techniques. It worked! But the noise problem still remained, and the result was useless in a noisy room. I had to come up with a noise suppression technique such as Chirp compression.

![](./doc/Chirp.jpg)

==> [Experiment2](EXPERIMENT2.md)

==> [Test code](./chirp)

## Ultrasonic communications experiment (Chirp modulation with compression)

This time, I employ some compression technique, and chirp sweep time corresponds to the length of time within time frame, unlike the previous experiment.

This material is great: https://www.ittc.ku.edu/workshops/Summer2004Lectures/Radar_Pulse_Compression.pdf

### Simulation of chirp compression on Jupyter Notebook

I made [chirp compression simulator](./simulation/ChirpSimulation.ipynb) on Jupyter Notebook.

The chirp signal is under white noise:

![upchirp_with_noise](./doc/Simulation_upchirp_with_noise.jpg)

I made FFT [upchirp * upchirp], then the chirp signal became identifiable at around zero Hz:

![upchirp_upchirp](./doc/Simulation_upchirp_upchirp.jpg)

### The first chirp compression experiment on STM32L4 DSP (June 10, 2018)

I tried Upchirp * Downchip before Upchirp * Upchirp. I transmitted very weak chirp signals to STM32L4 DSP with MEMS mic. It worked! But I observed two peaks most of time, since the FFT calculation was performed on a chirp signal split into two within the time frame, since the time frame was not in sync between the transmitter and the receiver.

![upchirp_downchirp](./doc/FFT_upXdown.jpg)

I should have tried Upchirp * Upchirp...

### How much time does it take to compute complex FFT of 2048 samples?

The measured value is 3msec for each complex FFT of 2048 samples at 80MHz system clock. 2048 samples correspond to 20.5msec at 100kHz sampling rate, so 3msec is short enough compared to 20.5msec.

### Next steps

#### Reference chirp signal generation at start up

Generate reference chirp on STM32L4 at start up.

#### Frame synchronization

Come up with some frame synchronization technique.

#### sinc filter optimization (moving average)

sinc5 filter reduces high-frequency noises more than sinc3. But it might not be so important...

## My original MEMS mic shield

I have bought [this MEMS mic](http://akizukidenshi.com/catalog/g/gM-05577/): Knowles SPM0405HD4H. The spec is similar to the mic on the expansion board from STMicro. Although this one does not support ultrasonic, it should be OK.

![Knowles](./doc/Knowles.jpg)

I am going to make my original shield with Knowles MEMS mic:

- Knowles MEMS mic
- LCDs
- LEDs
- Tactile switches
- CAN tranceiver
