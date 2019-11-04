# Change firmware for chinese USBISP to act as USBASP

Changes from original http://www.fischl.de/usbasp/

##### from
```
int main(void) {
  uchar i, j;

  // no pullups on USB and ISP pins
  PORTD = 0;
  PORTB = 0;
  // all outputs except PD2 = INT0
  DDRD = ~(1 << 2);
```

##### to
```
int main(void) {
  uchar i, j;
  
  // no pullups on USB and ISP pins
  PORTD = 0;
  PORTB = 0;
  // all PDx input
  // MK-USBISP v3.0
  DDRD = 0x00;
```

also every variable with PROGMEM at the beginning has to be constant with "const" before it
otherwise it won't compile with newer versions of avrdude

changes in makefile according to mcu
```
MEGA88 lfuse:0xff hfuse:0xdd
MEGA48 lfuse:0xff hfuse:0xdd
MEGA8  lfuse:0xef hfuse:0xc9
```
Converting the USBisp Programmer to a USBasp Programmer:

- Fetch Uwezi’s modified USBasp hex file from http://www.sciencetronics.com/greenphotons/?p=938 (The modification is the setting of the Data Direction bits for Port D to be all inputs.)
- Short the self-programming vias with a bit of wire.
- Connect to the master programmer, a USBasp, say, by means of the supplied cable with 10 pin connectors at each end.
- Erase the USBisp’s ATmega8 chip, if the lock bits prevent modification. (Erasure turned out not to be necessary on the USBisp I received.)
- Set the fuses correctly to hfuse=0xc9, lfuse=0xef.
- Flash the hex file "20130212_mega8_usbisp/default/20130212_mega8_usbisp.hex".
- Disconnect programmers and pull out the shorting wire.
- Connect the modified USBisp to the computer and use exactly as a USBasp.
- The red LED will become blue and the USB ID will have chaned from 03eb:c8b4 to 16c0:05dc.


Avrdude commands:
(fuses and mcu has to be set accordingly)
```
• avrdude -p m8 -c usbasp -U hfuse:w:0xc9:m
• avrdude -p m8 -c usbasp -U lfuse:w:0xef:m
• avrdude -p m8 -c usbasp -U flash:w:20130212_mega8_usbisp.hex:i
```
Reference: http://club.dx.com/reviews/151604/565379
