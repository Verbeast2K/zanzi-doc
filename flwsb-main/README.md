# FLWSB
Flexible LoRaWAN Sensor Board - Hardware design

this project was built upon a previous iteration. You can find the full documentation of this here: https://github.com/Dacetylan/FLWSB?fbclid=IwAR1HLporIOgVgvebDwlQVPCnp9HiQH27QhrYqrgZZ_8g4kAjzjsdf1E0I3I

# Mistakes that were corrected
- In the previous version of the FLWSB the RX an TX lines were connected incorrectly to the LoraWan module. In the new version the RX and TX lines were connected correctly.
- In the previous version there was a mistake in the silkscreen. Pin "PA09" had to be "PA07". This mistake was corrected in the new version.

# Changes made to the board
- Three leds were added to the board. One led will indicate whether the power supply is active. The other two leds were added on the RX and TX lines of the LoraWan module to indicate data transmission.
- Another type of voltage regulator was used on the new version. Instead of the LD1117S33TR voltage regulator the LDL1117S33R was used in this version. The new voltage regulator has a wider range of input voltages and has a lower quiescent current.

# New schematic
![FLWSB](assets/FLWSB-SAMDaaNo21.svg 'Figuur 1: New schematic of the FLWSB')

# PCB
![FLWSB](assets/FLWSB.png 'Figuur 2: 3D view of the new FLWSB version')

![FLWSB](assets/FLWSB_soldered.jpg 'Figuur 3: soldered FLWSB')
