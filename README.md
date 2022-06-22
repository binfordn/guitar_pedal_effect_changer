# Guitar Pedal Effect Changer

I was in charge of the effect changer module for my senior year capstone project, which was a guitar pedal that had different effects depending on the frequency. The following is taken from my group's final report.

### Pure Data Signal Processing

The selection and application of effects to the guitar signal takes place on the Raspberry Pi in a Pure Data program. The program our group wrote reads the guitar signal and the Arduino’s communication port output simultaneously. Depending on frequency range returned by the pitch detection algorithm running on the Arduino, the Arduino writes either a 0, a 1, or a 2 to its communication port. The Pure Data program uses the "comport" object to read this value and uses it as the selector input to a demultiplexer. The demultiplexer’s main input is the guitar signal coming from the ADC, read with the "adc~" object. Depending on the selector input, the signal is routed to one of three paths where the effects are applied. Each path allows one of three effects to be applied to that frequency range by interfacing with the GPIO. A separate "wiringPi_gpio" object is constantly reading the value of each physical switch in the switch network telling that effect to be applied to that range or not. This is done by multiplying the guitar sigal by the output of the GPIO. If the GPIO output is 1, the signal will be multiplied by 1 and sent to the appropriate effect patch. If the GPIO output is 0, the signal will be multiplied by 0, so nothing will be sent to that effect patch and that effect will not be applied. Thus, the multiplier for each effect in each range acts like a logical AND gate. The output of each effect patch is sent to the "dac~" object in the program, sending the final signal to the DAC.

The pedal has three possible effects that can be applied to the signal: fuzz, delay, and wah. The effects are implemented using patches, which are Pure Data blocks that use the language’s built-in functions to modify the signal.

### Fuzz Patch

The fuzz patch uses Pure Data’s "clip" function to limit the amplitude of the signal to a certain range. Cutting off the peaks of the waveform like this produces the distorted signal that is commonly associated with rock music.

### Delay Patch

The delay patch uses Pure Data’s "delread" and "delwrite" functions to create delayed copies of the signal and send them to the output after the original signal. This results in hearing that note repeated multiple times until the signal fades out, like an echo. We experimented with how much time passes between each delayed copy until we found a value that seemed pleasant for the purpose of songwriting.

### Wah Patch

The wah patch uses Pure Data’s "vcf" (voltage-controlled band-pass filter) and "osc" (oscillator) functions to create what is formally called a "spectral glide," which makes the note mimic a human voice saying "wah wah." This is achieved by oscillating between peak responses of a frequency filter. Like with the delay patch, we experimented with how much time one oscillation between the minimum and maximum of the frequency range took until we found a value that sounded pleasing to us.

### PureData WiringPi Library

http://nyu-waverlylabs.org/rpi-gpio/
