# bm_ctrl_codes
Control Interface Backes Müller Active Speakers

## Hardware

#### RS-232 of Backer & Müller Active Speakers "BM-Line 12"
Thanks to their internal signal processing these speaker provide the option of programming frequency response into their DSP to compensate for room acoustics. The benefit of doing this in the speakers and not in the amp is phase linearity. Linear phase is always preserved and not touched by any setting.

Additionally it is possible to adjust the sweet spot by adding some delay to one channel. In practice this setting has proven to have a great effect on the sound. Even a slight missadjustment destroys the impression of sound comming exactly from the midst "and not from the speakers".

The speakers have a [RJ-45](https://en.wikipedia.org/wiki/Modular_connector#8P8C) plug for remote control and programming. but it's not Ethernet, its RS-232. Each speaker has a control input and a control output for daisy-chaining. Each speaker has a number: 1 for left, 2 for right. All this and much more is coded on the little SD-card on the back-side.

In contrast to normal speakers which can be exchanged without any effect, these speakers "know" about their channel, left or right. This is necessary when driving them with a common AES digital signal, which covers both channels. Then each speaker plays the correct channel out of the digital audio data stream.

| Pin | Color | Function |
| --- | --- | --- |
| 1   | blue | none |
| 2   | orange | none |
| 3   | black | GND |
| 4   | red | data to speaker |
| 5   | green | data from speaker |
| 6   | yellow | +5 V supply |
| 7   | brown | none |
| 8   | white | none |

You have to build an adapter that connects these lines to a standard SUB-D connector.

Data level is +/- 6 V = 12 Vss. Serial parameters are 115.2 kBaud, 1 start bit, 8 data bits, no parity, 1 stop bit. The data is pure ASCII with only 7 bits. Since the MSB is always 0 in this case, the serial parameters could also be 1 start bit, 7 data bits, no parity, 2 stop bits.

Each speaker accepts a query or config line and always responds with a status line.

- Query line to speaker: **\<speaker no> \<setting>CRLF**
  
- Config line to speaker: **\<speaker no> \<setting> \<parameters>CRLF**
  
- Status line from speaker: **\<speaker no> \<setting> \<parameters>CRLF**
  

###### \<speaker no>

"#1" for left, "#2" for right speaker. A query or config line may also start with "#0", which addresses each speaker. This will generate two status lines as response, one starting with "#1" from the left speaker, and one starting with "#2" from the right.

###### \<setting> and \<parameters>

| setting | parameters | meaning |
| --- | --- | --- |
| VOLUME0 | -120.0 ... 24.0 | Gain / Volume in dB.<br/>Always 0 after power on.<br/>Different values serve as balance control. |
| EQx | 1 \<F> \<G> \<Q> | One of the 10 parametric equalizers x = 0 ... 9.<br/>F = frequency in Hz, 20 ... 20000<br/>G = gain in dB, -12.0 ... 12.0<br/>Q = Q factor, 0.1 ... 20.0<br/>Usually same parameters for both speakers. |
| EQx | 2 \<F> \<G> | Low frequency (Bass).<br/>F = below this frequency boost/damp starts over 3 octaves.<br/>G = final boost/damp in dB, -12.0 ... 12.0.<br/>e.g. with F = 400 Hz and G = +12 dB level is +4 dB @ 200 Hz, +8 dB @ 100 Hz and +12 dB below 50 Hz |
| EQx | 3 \<F> \<G> | High frequency (Treble).<br/>F = above this frequency boost/damp starts over 3 octaves.<br/>G = final boost/damp in dB, -12.0 ... 12.0.<br/>e.g. with F = 1 kHz and G = -3 dB level is -1 dB @ 2 kHz, -2 dB @ 4 kHz and -3 dB above 8 kHz |
| DELAY0 | 0.0 ... 30.0 | If one speaker is closer to the listener than the other, its signal can be delayed as a compensation in milliseconds.<br/>Usually this value should only be applied on one of both speakers.<br/>30 milliseconds are quivalent to 10 meters. |
| SUB0 | 0 0.0 | For additional subwoofer(s). On/Off and level. |
