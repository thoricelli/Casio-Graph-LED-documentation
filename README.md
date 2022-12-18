<h1><b>Reminder:</b></h1>
<b>This is not to support cheating. It is merely to document the LED on a hardware/software level.
<br>This can be used for custom OS's which want to integrate a proper examination mode that abide by the rules.
<br>Cheating can get you banned from any examination center.</b>

<h1>Casio Graph LED documentation</h1>
The OS used in my disasembly is: version 2.05.
<br><br>
The LED is controlled by what is called the "C-specification module" or "CMOD" in Casio's emulator files.
<br>See <a href="https://bible.planet-casio.com/yatis/hardware/sh7305/registers_list.html#cmod---c-specification-module">Yatis' documentation</a> for more information.
<br>Two addresses are used:
<br><br>
<ul>
<li>0xa44c0020 (CMOD.DDCK_CNTR)</li>
<li>0xa44c0000 (CMOD.DDCLKR0)</li>
</ul>
<h2>CMOD.DDCK_CNTR</h2>
CMOD.DDCK_CNTR is the external CLK1 setting and it is an 8 bit address.<br>
If set incorrectly it can cause the calculator to become unstable.<br><br>
The setting used to control the LED is 0x4 (00000100).<br>
And is set to 0x5 when not used. (00000101)<br>

<h2>CMOD.DDCLKR0</h2>
CMOD.DDCLKR0 is a 16 bit address.
<br>When the DDCK_CNTR is correctly set, we can adjust the brightness, flicker or enable/disable the LED.
<br><br>
<table>
  <tr>
    <th>Bit</th>
    <th>Name (made up)</th>
    <th>Mode</th>
    <th>Description</th>
  </tr>
  <tr>
    <th>15</th>
    <th>???</th>
    <th>???</th>
    <th>Is always set to 1 by OS, but behaviour is unkown.</th>
  </tr>
  <tr>
    <th>14</th>
    <th>LED_toggle</th>
    <th>R/W</th>
    <th>Enable/disable bit for LED</th>
  </tr>
  <tr>
    <th>13</th>
    <th>LED_ftime1</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>12</th>
    <th>LED_ftime2</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>11</th>
    <th>LED_ftime3</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>10</th>
    <th>LED_ftime4</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>9</th>
    <th>LED_ftime5</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>8</th>
    <th>LED_ftime6</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>7</th>
    <th>LED_ftime7</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>6</th>
    <th>LED_ftime8</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>5</th>
    <th>LED_ftime9</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>4</th>
    <th>LED_ftime10</th>
    <th>R/W</th>
    <th>Light time for flicker?</th>
  </tr>
  <tr>
    <th>3</th>
    <th>LED_brightness1</th>
    <th>R/W</th>
    <th>Sets the brightness of the LED? (unsure)</th>
  </tr>
  <tr>
    <th>2</th>
    <th>LED_brightness2</th>
    <th>R/W</th>
    <th>Sets the brightness of the LED.</th>
  </tr>
  <tr>
    <th>1</th>
    <th>LED_brightness3</th>
    <th>R/W</th>
    <th>Sets the brightness of the LED.</th>
  </tr>
  <tr>
    <th>0</th>
    <th>LED_brightness4</th>
    <th>R/W</th>
    <th>Sets the brightness of the LED.</th>
  </tr>
</table>

<br>
The brightness, and flicker is kind of a weird one.<br>
They don't seem to be increasing values, eg brightness from 1-15 (or 7).
<br><br>
Anyways, here are 3 values used by the LED test in diagnostic mode:<br>
Minimum - 011<br>
Middle - 001<br>
Maximum - 101<br>

<h2>Usage in code</h2>

Thanks to the <a href="https://gitea.planet-casio.com/Lephenixnoir/fxos">FXOS dissasembler</a>.<br>
A snippet of the LEDon function used in Diagnostic mode:
<br>
```
mov.l   r14, @-r15
sts.l   pr, @-r15
mov.l   0xa4050100 PFC.PACR, r5
mov.w   0x000003fc, r14
mov     #1, r2
mov     #1, r4
shll8   r2
mov.w   @r5, r1
and     r14, r1
mov.w   r1, @r5
add     #78, r5
mov.l   0xa44c0020 CMOD.DDCK_CNTR, r14
mov.w   0xfffffcff, r1
mov.w   @r5, r7
and     r1, r7
or      r2, r7
mov.w   r7, @r5
mov.b   @r14, r0
mov.l   0x8003ffdc, r7
and     #-2, r0
jsr     @r7
mov.b   r0, @r14
bsr     <0x800427e8>
mov     #-1, r4
mov.l   0x801d1eb8, r1
shll    r0
mov.l   0xa44c0000 CMOD.DDCLKR0, r2
mov     #1, r4
mov.l   0x8003ffdc, r5
mov.w   @(r0,r1), r6
jsr     @r5
mov.w   r6, @r2
mov.b   @r14, r0
or      #1, r0
mov.b   r0, @r14
lds.l   @r15+, pr
rts
mov.l   @r15+, r14
```

Simplified, and unrelevant commands removed:

```
#DDCK_CNTR #1
mov.l   0xa44c0020 CMOD.DDCK_CNTR, r14  #Move 0xa44c0020 to r14
mov.b   @r14, r0                        #Move value at 0xa44c0020 to r0
and     #-2, r0                         #AND r0 with decimal -2 (11111110)
mov.b   r0, @r14                        #Move result (r0) to memory location 0xa44c0020

#DDCLKR0
bfda   bsr     <0x800427e8>             #Move to function
|
-----> mov     #1, r0                   #Move decimal 1 into r0
mov.l   0x801d1eb8, r1                  #Move 0x801d1eb8 into r1
shll    r0                              #Bitwise-Shift R0 to the left
mov.l   0xa44c0000 CMOD.DDCLKR0, r2     #Move 0xa44c0000 to r2
mov.w   @(r0,r1), r6                    #Move value at location 0x801d1eb8+0x2 to r6 (1100000000000011)
mov.w   r6, @r2                         #Move r6 to memory location 0xa44c0000

#DDCK_CNTR #2
mov.l   0xa44c0020 CMOD.DDCK_CNTR, r14  #Move 0xa44c0020 to r14
mov.b   @r14, r0                        #Move byte value at 0xa44c0020 to r0
or      #1, r0                          #OR r0 with decimal 1 (00000001)
mov.b   r0, @r14                        #Move result (r0) to memory location 0xa44c0020
```

Now in C code:

```C
char*CMOD_DDCK_CNTR=(void*)0xa44c0020;      //Word
short*CMOD_DDCLKR0=(void*)0xa44C0000;       //Byte

void TurnONLED() {
	*CMOD_DDCK_CNTR &= 0xFE;            //11111110
	*CMOD_DDCLKR0 = 0xc003;             //1100000000000011
	*CMOD_DDCK_CNTR |= 0x01;            //00000001
}
```

We can do the opposite for turning the LED off:

```C
void TurnOFFLED() {
	*CMOD_DDCK_CNTR &= 0xFE;            //11111110
	*CMOD_DDCLKR0 = 0x0;                //0000000000000000
	*CMOD_DDCK_CNTR |= 0x01;            //00000001
}
```

However the LED will automatically be turned off.
<br>What code is responsible for turning it off automatically, I yet have to find.
<br>
<br>To circumvent this issue we can do the following:

```C
SetAutoPowerOffFlag(); //Syscall 0x48D
Cursor_SetFlashOff(); //Syscall 0x812
```

This is what is used in the beginning by diagnostic mode. I've yet to find how the examination mode circumvents this issue.<br>
But it will break the USB timer, so you wont get a popup anymore when connecting your calculator.
