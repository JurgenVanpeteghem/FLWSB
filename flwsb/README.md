# Flexible LoRaWAN Sensor Board SAMDaaNo21

Het FLWSB Main Board is een microcontroller bord gebaseerd op de ATSAM D21 in de vormfactor vergelijkbaar met een Arduino Nano. Het moet gemakkelijk zijn om dit bord in een breadboard te prikken en zo testen uit te voeren of schakelingen te bouwen. 

![SAMDaaNo21](assets/SAMDaaNo21-pinout.svg)

[PDF versie van de pinout diagram](./assets/SAMDaaNo21-pinout.pdf ':ignore')

![SAMDaaNo21-horizontaal](assets/SAMDaaNo21-horizontaal.JPG)



## KiCad bibliotheken

Het is handig om een printplaat te kunnen ontwikkelen waar de SAMDaaNo21 opgeprikt kan worden.

Om dit proces te vergemakkelijken is er een KiCad bibliotheek ontwikkeld, die bevat een symbol, footprint en 3D-model.

Het is aangeraden om bibliotheken die niet standaard in KiCad zitten mee in de projectmap te steken en te importeren als projectbibliotheek. Als je je projectmap dan deelt met derden zullen zij meteen beschikken over alle nodige bibliotheken.

Tutorial: [How to import into KiCad V6 & later?](https://support.snapeda.com/en/articles/5995733-how-to-import-into-kicad-v6-later)

[SAMDaaNo21 bibliotheek bestanden](https://github.com/Dacetylan/FLWSB/tree/SAMDaaNo21/src/printed-circuit-boards/FLWSB-SAMDaaNo21-KiCad-lib)

![LWSB-SAMDaaNo21-v1-symbol](assets/FLWSB-SAMDaaNo21-v1-symbol.svg 'SAMDaaNo21 symbol')
![LWSB-SAMDaaNo21-v1-footprint](assets/FLWSB-SAMDaaNo21-v1-footprint.svg 'SAMDaaNo21 footprint')

## Schema

Het elektrisch schema van dit bord ziet er als volgt uit:

![Figuur 1: FLWSB-SAMDaaNo21 schema](./assets/FLWSB-SAMDaaNo21.svg 'Figuur 1: FLWSB-SAMDaaNo21 schema')

Alle onderdelen worden verder toegelicht.

![Figuur 2: Blokdiagram werking SAMDaaNo21](./assets/blokdiagram.svg 'Figuur 2: Blokdiagram werking SAMDaaNo21')


[ATSAM D21 Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/SAM-D21DA1-Family-Data-Sheet-DS40001882G.pdf)

## Testpunten

Er zijn verschillende testpunten toegevoegd om op een gemakkelijke en toegankelijke manier verschillende parameters op het bord te kunnen meten.

Er is gekozen voor through hole testpunten zodat het gemakkelijk is om deze met een probe te meten. Alle testpunten zijn te vinden nabij de USB-poort.

| Testpunt nummer | Verbinding                                                   |
| --------------- | ------------------------------------------------------------ |
| TP1             | VBUS: 5 V afkomstig van USB                                  |
| TP2             | VDD: 3,3 V gereguleerde spanning                              |
| TP3             | VIN: Ingangsspanning voordat het naar de lineaire regelaar gaat |



## LoRaWAN Module

![Figuur 3: LoRaWAN module](./assets/LoRaWAN.svg 'Figuur 3: LoRaWAN module')

Er is een RN2483 module mee geïmplementeerd op de printplaat. Deze is aangesloten op de UART bus van SERCOM 2.

De RN2483 bevindt zich aan het uiteinde. Er kan een antenne aangesloten worden via een U.FL connector.  

[Productpagina connector](https://be.farnell.com/hirose-hrs/u-fl-r-smt-1-10/rf-coaxial-u-fl-straight-jack/dp/1688077)


## USB Interface & VIN

![Figuur 4: USB Interface & VIN schema](./assets/usb-vin.svg 'Figuur 4: USB Interface & VIN schema')

Het schema voor de USB-interface is gebaseerd op een voorbeeld uit een Atmel handleiding. De zener diodes die zichtbaar zijn zorgen ervoor dat de kans op schade door elektrostatische ontlading (ESD) beperkt wordt.

![Figuur 5: USB Interface & VIN schema](./assets/usb-voorbeeld.svg 'Figuur 5: USB Interface & VIN schema')

[Ref USB-applications note](https://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-42261-SAM-D21-USB_Application-Note_AT06475.pdf)

[Ref schematic checklist](https://ww1.microchip.com/downloads/en/DeviceDoc/SAM-D21DA1-Family-Data-Sheet-DS40001882G.pdf#_OPENTOPIC_TOC_PROCESSING_d10240e380866)



De Schottky diode (D6) is er om de USB-poort van je laptop te beschermen wanneer het bord van stroom wordt voorzien via de VIN pin. Het zou kunnen dat aan de VIN pin een hogere spanning wordt geleverd dan 5 V waardoor dit potentiaalverschil ongewenste stroom kan laten vloeien. Hetzelfde principe wordt toegepast bij de ESP32 DEVKIT.

`TP3` is een testpunt dat aanwezig is om op een toegankelijke manier de VIN-spanning te meten.

[Ref ESP32 schema](https://dl.espressif.com/dl/schematics/esp32_devkitc_v4-sch-20180607a.pdf)


Het bordje kan voorzien worden van stroom via de USB-C poort. Een LDO (Low Dropout regelaar) zal de spanning afkomstig van de computer reguleren naar een stabiele 3,3 V.

De LDO is een LD1117S33 en ondersteunt een minimale ingangsspanning van 4,3 V en een maximale van 15 V. Dat kan worden aangelegd via de VIN pin. Er is een Schottky diode voorzien om ervoor te zorgen dat de spanning van VIN niet op de USB-poort komt te staan.

*Let op dat je met een lineaire regelaar te maken hebt. Hoe hoger de ingangsspanning, hoe warmer de LDO zal worden!*

Het is ook mogelijk om de regelaar over te slaan en rechtstreeks 3,3 V te leveren via de 3V3 pin. Dit kan handig zijn bij een situatie waarin we zo zuinig mogelijk willen zijn en een efficiëntere 3,3 V regelaar gebruiken van een andere pcb. *Belangrijk is dat deze spanning niet te hard mag afwijken van 3,3 V!*



## RESET & DEBUG

![Figuur 6: RESET & DEBUG schema](./assets/reset-debug.svg 'Figuur 6: RESET & DEBUG schema')

De reset knop is opgebouwd zoals in de datasheet van de ATSAM D21 wordt weergegeven onder "Schematic Checklist Figure 45-4. External Reset Circuit Schematic".

Als er nog geen bootloader op de ATSAMD staat of als we een bare metal installatie gebruiken zullen we gebruik moeten maken van de program en debug header. Dat zijn de 3 pinnen centraal op de printplaat voor de LoRaWAN module. De pinout van links naar recht is: RESET, SWDIO, SWCLK. 

De ATSAMD maakt gebruik van Serial Wire Debugging (SWD), dat maakt het mogelijk om via fuses in te stellen, binaries in te laden en zelfs break-points aan je code toe te voegen.


## Low Dropout Lineair Regulator

![Figuur 7: Low Dropout Lineair Regulator schema](./assets/ldo.svg 'Figuur 7: Low Dropout Lineair Regulator schema')

In dit geval wordt er een LD1117 3,3 V regelaar gebruikt. Deze regelaar kan met een vrij groot ingangsbereik (tot 15 V) een uitgangsspanning van 3,3 V leveren.

`TP2` is een testpunt dat aanwezig is om op een toegankelijke manier de 3.3 V gereguleerde spanning te meten.

[LD1117 datasheet](https://www.st.com/resource/en/datasheet/ld1117.pdf)

## Power Supply Connections

![Figuur 8: Power Supply Connections schema](./assets/power.svg 'Figuur 8: Power Supply Connections schema')

Het schema voor de ontkoppelcondensatoren en spoel voor de ATSAM D21 komt ook rechtstreeks uit de datasheet. Wat belangrijk is bij deze componenten is dat alles rechts van de blauwe stippellijn zo dicht mogelijk bij de vermelde pinnen staat. Deze condensatoren vangen kleine storingen en rimpels op die zich voordoen in de bronspanning.

## Input / Output

![Figuur 9:  Input / Output schema](./assets/input-output.svg 'Figuur 9:  Input / Output schema')

Dit zijn alle GPIO, SERCOM en power aansluitingen die via pinheaders naar buiten worden gebracht. De SERCOM poorten zijn alvast gedefinieerd zodat onder andere de pull-up weerstanden geplaatst kunnen worden. Alle andere poorten zijn voor algemeen gebruik. In de datasheet van de ATSAM D21 onder 7. I/O Multiplexing and Considerations kunnen alle verschillende mogelijkheden van deze aansluitingen gevonden worden (AC, ADC, DAC, PTC, DAC, IO, ...).

PA12 en PA13 zijn onderdeel van de I²C bus en hebben al een pull-up weerstand van 4,7 K voorzien. *Bij het plaatsen van nog externe pull-up weerstanden zullen zij in parallel staan en zal de weerstandswaarde te laag worden.*


| I/O Pin | Pin      | SERCOM                  | SERCOM-ALT       | External Interrupt | ADC, DAC, AC, REF    | Peripheral Touch Controller | TC/TCC         | TCC            | COM               | Generic Clock Generator |
| ------- | -------- | ----------------------- | ---------------- | ------------------ | -------------------- | --------------------------- | -------------- | -------------- | ----------------- | ----------------------- |
| GND     | PowerPin | Ground Reference        |                  |                    |                      |                             |                |                |                   |                         |
| PA02    | 3        |                         |                  | EXTINT:2           | ADC:0<br/>VOUT       | Y:0                         |                | TCC3/<br/>WO:0 |                   |                         |
| PA03    | 4        |                         |                  | EXTINT:3           | ADC:1<br/>VREFA      | Y:1                         |                | TCC3/<br/>WO:1 |                   |                         |
| PB08    | 7        |                         | SCOM4/<br/>PAD:0 | EXTINT:8           | ADC:2                | Y:14                        | TC4/<br/>WO:0  | TCC3/<br/>WO:6 |                   |                         |
| PB09    | 8        |                         | SCOM4/<br/>PAD:1 | EXTINT:9           | ADC:3                | Y:15                        | TC4/<br/>WO:1  | TCC3/<br/>WO:7 |                   |                         |
| PA04    | 9        |                         | SCOM0/<br/>PAD:0 | EXTINT:4           | ADC:4<br/>AC:0 VREFB | Y:2                         | TCC0/<br/>WO:0 | TCC3/<br/>WO:2 |                   |                         |
| PA05    | 10       |                         | SCOM0/<br/>PAD:1 | EXTINT:5           | ADC:5<br/>AC:1       | Y:3                         | TCC0/<br/>WO:1 | TCC3/<br/>WO:3 |                   |                         |
| PA06    | 11       |                         | SCOM0/<br/>PAD:2 | EXTINT:6           | ADC:6<br/>AC:2       | Y:4                         | TCC1/<br/>WO:0 | TCC3/<br/>WO:4 |                   |                         |
| PA07    | 12       |                         | SCOM0/<br/>PAD:3 | EXTINT:7           | ADC:7<br/>AC:3       | Y:5                         | TCC1/<br/>WO:1 | TCC3/<br/>WO:5 | I2S/<br/>SD:0     |                         |
| PA08    | 13       | SCOM0/<br/>PAD:0        | SCOM2/<br/>PAD:0 | NMI                | ADC:16               | X:0                         | TCC0/<br/>WO:0 | TCC1/<br/>WO:2 | I2S/<br/>SD:1     |                         |
| PA09    | 14       | SCOM0/<br/>PAD:1        | SCOM2/<br/>PAD:1 | EXTINT:9           | ADC:17               | X:1                         | TCC0/<br/>WO:1 | TCC1/<br/>WO:3 | I2S/<br/>MCK:0    |                         |
| PA10    | 15       | SCOM0/<br/>PAD:2        | SCOM2/<br/>PAD:2 | EXTINT:10          | ADC:18               | X:2                         | TCC1/<br/>WO:0 | TCC0/<br/>WO:2 | I2S/<br/>SCK:0    | GCLK:4                  |
| PA11    | 16       | SCOM0/<br/>PAD:3        | SCOM2/<br/>PAD:3 | EXTINT:11          | ADC:19               | X:3                         | TCC1/<br/>WO:1 | TCC0/<br/>WO:3 | I2S/<br/>FS:0     | GCLK:5                  |
| PA12    | 21       | SCOM2/<br/>PAD:0        | SCOM4/<br/>PAD:0 | EXTINT:12          | AC:0                 |                             | TCC2/<br/>WO:0 | TCC0/<br/>WO:6 |                   |                         |
| PA13    | 22       | SCOM2/<br/>PAD:1        | SCOM4/<br/>PAD:1 | EXTINT:13          | AC:1                 |                             | TCC2/<br/>WO:1 | TCC0/<br/>WO:7 |                   |                         |
| VIN     | PowerPin | LDL1117S33 4.3 V - 15 V |                  |                    |                      |                             |                |                |                   |                         |
| 3V3     | PowerPin | 3.3V LDO output         |                  |                    |                      |                             |                |                |                   |                         |
| GND     | PowerPin | Ground Reference        |                  |                    |                      |                             |                |                |                   |                         |
| PB03    | 48       |                         | SCOM5/<br/>PAD:1 | EXTINT:3           | ADC:11               | Y:9                         | TC6/<br/>WO:1  | TCC3/<br/>WO:3 |                   |                         |
| PB22    | 37       |                         | SCOM5/<br/>PAD:2 | EXTINT:6           |                      |                             | TC7/<br/>WO:0  | TCC3/<br/>WO:0 |                   | GCLK:0                  |
| PA23    | 32       | SCOM3/<br/>PAD:1        | SCOM5/<br/>PAD:1 | EXTINT:7           |                      | X:11                        | TC4/<br/>WO:0  | TCC0/<br/>WO:5 | USB/<br/>SOF 1KhZ | GCLK:7                  |
| PA22    | 31       | SCOM3/<br/>PAD:0        | SCOM5/<br/>PAD:0 | EXTINT:6           |                      | X:10                        | TC4/<br/>WO:1  | TCC0/<br/>WO:4 |                   | GCLK:6                  |
| PA21    | 30       | SCOM5/<br/>PAD:3        | SCOM3/<br/>PAD:3 | EXTINT:5           |                      | X:9                         | TC7/<br/>WO:1  | TCC0/<br/>WO:7 | I2S/<br/>FS:0     | GCLK:5                  |
| PA20    | 29       | SCOM5/<br/>PAD:2        | SCOM3/<br/>PAD:2 | EXTINT:4           |                      | X:8                         | TC7/<br/>WO:0  | TCC0/<br/>WO:6 | I2S/<br/>SCK:0    | GCLK:4                  |
| PA19    | 28       | SCOM1/<br/>PAD:3        | SCOM3/<br/>PAD:3 | EXTINT:3           | AC:1                 | X:7                         | TC3/<br/>WO:1  | TCC0/<br/>WO:3 | I2S/<br/>SD:0     |                         |
| PA18    | 27       | SCOM1/<br/>PAD:2        | SCOM3/<br/>PAD:2 | EXTINT:2           | AC:0                 | X:6                         | TC3/<br/>WO:0  | TCC0/<br/>WO:2 |                   |                         |
| PA17    | 26       | SCOM1/<br/>PAD:1        | SCOM3/<br/>PAD:1 | EXTINT:1           |                      | X:5                         | TCC2/<br/>WO:1 | TCC0/<br/>WO:7 |                   | GCLK:3                  |
| PA16    | 25       | SCOM1/<br/>PAD:0        | SCOM3/<br/>PAD:0 | EXTINT:0           |                      | X:4                         | TCC2/<br/>WO:0 | TCC0/<br/>WO:6 |                   | GCLK:2                  |
| PA15    | 24       | SCOM2/<br/>PAD:3        | SCOM4/<br/>PAD:3 | EXTINT:15          |                      |                             | TC3/<br/>WO:1  | TCC0/<br/>WO:5 |                   | GCLK:1                  |
| PA14    | 23       | SCOM2/<br/>PAD:2        | SCOM4/<br/>PAD:2 | EXTINT:14          |                      |                             | TC3/<br/>WO:0  | TCC0/<br/>WO:4 |                   | GCLK:0                  |




### Legende

`SERCOM`: Serial Communication, keuze tussen: USART, I²C, SPI of SMBus

`EXTINT[n]`: External Interrupts

`AIN[n]`: ADC Analog Inputs

`X[n]`, `Y[n]`: Peripheral Touch Controller - PTC Input

`TC4/WO[n]`, `TC6/WO[n]`: Waveform/PWM Outputs

`TCC3/WO[n]`: Waveform/PWM Outputs/ Capture Inputs

`AC/CMP[n]`: AC Comparator Outputs

`I2S/FS[n]`: Inter-IC Sound Controller, I²S Word Select or TDM Frame Sync

`GCLK_IO[n]`: Generic Clock (source clock or generic clock generator output)


## Toekomstige verbeteringen

- Momenteel zijn er geen mounting holes aanwezig, dat vonden we  nog niet prioritair.
  - Mouning holes of inkepingen aan de randen van de printplaat zijn dus nog mogelijk en kunnen ook zeker van pas komen.
- De impedantie van het pad naar de antenne-aansluiting is niet theoretisch berekend. Dit is iets waar in een volgende versie over kan worden nagedacht.
- Er is geen LED aanwezig.
- Op het silkscreen staat een fout, `PA07` staat op het silkscreen als `PA09`. 
- Het schema bevat een fout die de LoRaWAN module onbruikbaar maakt. RX en TX van de UART-verbinding moeten omgewisseld worden.
  
### Tijdelijke oplossing LoRaWAN module

Een UART-verbinding heeft een TX (transmit) en RX (receive) aansluiting nodig. Logischerwijze zal je dus, bij een verbinding tussen twee apparaten, RX en TX met elkaar verbinden. Bij het ontwerpen van dit schema is door onoplettendheid TX met TX verbonden en RX met RX verbonden.

De PINMUX van de ATSAM D21 kan iedere SERCOM pin als RX instellen, maar niet iedere pin als TX schakelen. Dit maakt het onmogelijk om de oplossing geheel softwarematig te zoeken. Enkel PAD0 en PAD2 van een SERCOM poort kan je instellen als TX.

Wij hebben `PA8` (`S2TX` op het silkscreen) ingesteld als RX omdat deze verbonden is met de TX van de LoRaWAN module. `PA09` (`S2RX` op het silkscreen) hebben we hoog impedant gezet (`INPUT`) en kortgesloten met `PA10`. `PA10` is PAD2 van SERCOM2 dus kan ingesteld worden als nieuwe TX.

Het resultaat is dat we nu het baantje naar de RESET pin van de LoRaWAN module moeten onderbreken, omdat `PA10` verbonden is met deze pin. Die onderbreking is het gemakkelijkste te maken met een mesje of platte schroevendraaier net op de plek waar deze richting het pad van de module gaat.

De aanpassingen van deze pinnen is gemaakt in de ArduinoCore dus er hoeft in de code niets gewijzigd te worden.

Er is geen manier meer om de module te resetten vanaf de MCU, moest dat nodig zijn, dan kan er een kabeltje gesoldeerd worden naar een van de vrije GPIO pinnen.



## Bill of Materials

[Bill of materials ibom](./bom/ibom.html ':include :type=iframe width=100% height=1024px')
