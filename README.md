# Lab Power Supply

An adjustable power supply powered by a laptop charger. Features knobs for changing the voltage and current limit, voltage and ampere meter, button to show the current limit on the meter. The circuit is fully analog and features no firmware or microcontroller.

<img width="1237" height="860" alt="schematic" src="https://github.com/user-attachments/assets/e1461076-5935-4376-a9b0-28be66403d5d" />

<img width="755" height="611" alt="pcb" src="https://github.com/user-attachments/assets/0bc784c5-f4d1-4719-9ee5-6421344fd797" />

<img width="982" height="779" alt="pcb_render_front" src="https://github.com/user-attachments/assets/f5844844-ad2b-4647-99df-499875fab8da" />

<img width="1005" height="805" alt="pcb_render_back" src="https://github.com/user-attachments/assets/16eaa5f7-fba8-4129-ba6c-7dbc0e6d9098" />

<img width="1192" height="840" alt="enclosure" src="https://github.com/user-attachments/assets/b17de0fe-25d0-41c3-9638-fe555e1836e3" />

## Features

- Customizable output from 1.25V to about 16.5 volts. 
- Customizable current limit from 0 ampers to about 2.2 ampers.
- Red LED that lights up when the supply is limiting.
- Meter that shows the output voltage and current.
- OUTPUT switch for toggling the outputs.
- SHOW LIMIT button that shows the current limit instead of the voltage on the display.
- Reverse polarity protection.
- Resettable fuse on the input
- Undervoltage lockout
- Runs off a standard 19V laptop charger w/ a 5.5/2.5mm jack.
- Fully THT, no complicated SMD soldering.
- 3D printed enclosure with vents for heat, tilted feet and knobs.

## Motivation

Thinking about what I was going to make for week 5, I wanted something that would be genuinely useful for my future tinkering. Recently, I was reverse engineering a Cisco switch and needed a source of 3.3V. Thankfully, my Raspberry Pi Pico was able to deliver enough, but it was close to not being enough. This would be a good solution for the future!

## Functionality

- The laptop charger plugs into a DC jack on the back of the enclosure. This is wired to a screw terminal on the PCB. Power goes through a resettable fuse and a P-MOSFET for reverse polarity protection.
- An LM2576T-ADJ steps the voltage down. There are two 1k parallel resistors and a 10k potentiometer (VOLTAGE) for the feedback divider.
- A TL431 makes a 2.495 volt reference and one LM324 compares it with a third of the input voltage. If it goes below about 6.8V, the regulator turns off. Above about 8.8V, it turns back on (with hysteresis). This fulfills undervoltage lockout.
- An 0.1 ohm shunt resistor sits in the output's negative path for current sensing. An op-amp amplifies its voltage 11 times.
- A second op-amp compares that with the setting of the CURRENT knob. If the current is too high, current enters the LM2576 feedback pin through the red LED and a 1k resistor, which forces the regulator to lower output. The LED also blocks current from flowing back.
- The SHOW LIMIT button uses a third op-amp to switch the meter's voltage wire between the output and the current limit signal.
- Made only out of 1k/10k resistors to save costs as my local electronics shop has minimum order quantities on each.

## How it was made

All electrical work was done in KiCad and all CAD work was done in Autodesk Fusion. I ran into some issues:

- Originally I planned to use a TL431 to drive the LM2576's ON/OFF pins directly, but the datasheet of the LM2576 says the pin has to go below about 1V to turn it on. However, a TL431 can't get its output below about 2V, so the regulator might never turn on. I switched the LM358 for a LM324 which was even cheaper.
- I had 1k resistor for the green power LED but it is only rated for 7mA, which would come out to about 17 mA at 18V. I added 2 more 1K resistors in series.
- I wired the CURRENT potentiometer backwards which would mean turning it clockwise would lower the limit.
- When routing, I first connected the current sensing input inefficiently which would increase resistance and make the current limit kick in too early.
- The ground pour didn't reach all pads so I needed to add some vias and tracks.
- In Fusion, I often swapped axis and built the tray on the wrong side of the components.

## Tech stack

- KiCad for the schematic and PCB
- Autodesk Fusion for the enclosure and knobs
- JLCPCB for the board, GM Electronic for the parts

## Repo layout

- pcb/ contains all KiCad work, including the project, PCB, footprints and schematic
- pcb/gerbers contains gerber, drill files and a zip file for the gerbers.
- cad/ contains the Fusion files and STEP files
- stl/ contains the printable parts
- images/ contains renders and screenshots

## Bill of materials

| Part | Qty | Source | Unit | Total |
|---|---|---|---|---|
| [PCB Manufacturing (5 pcs)](https://jlcpcb.com) | 1 | JLCPCB | $4.00 | $4.00 |
| [TAEJIN-HTC LM2576T-ADJ regulator](https://www.gme.cz/v/1519044/taejin-htc-korea-lm2576t-adj-vykonovy-spinany-regulatorstep-down) | 1 | GM Electronic | $2.31 | $2.31 |
| [TALEMA DPO-3.0-100 inductor](https://www.gme.cz/v/1494058/talema-dpo-30-100-tlumivka) | 1 | GM Electronic | $3.07 | $3.07 |
| [SB540 Schottky diode](https://www.gme.cz/v/1514436/sb540-yj-schottkyho-dioda) | 2 | GM Electronic | $0.45 | $0.90 |
| [V7143 heatsink](https://www.gme.cz/v/1499787/v7143-black-chladic) | 1 | GM Electronic | $1.56 | $1.56 |
| [Infineon IRF9540N MOSFET](https://www.gme.cz/v/1493222/infineon-irf9540npbf-unipolarni-tranzistor) | 1 | GM Electronic | $0.61 | $0.61 |
| [TL431ACZ reference](https://www.gme.cz/v/1493687/tl431acz) | 1 | GM Electronic | $0.28 | $0.28 |
| [Capacitor 470 microFarad 35V](https://www.gme.cz/v/1486880/hitano-ce-470u-35v-hit-ecr-10x16-rm5-bulk-elektrolyticky-kondenzator) | 5 | GM Electronic | $0.18 | $0.90 |
| [Capacitor 100 nanoFarad](https://www.gme.cz/v/1520318/ck-100n-50v-x7r-rm508-10-keramicky-kondenzator) | 8 | GM Electronic | $0.09 | $0.72 |
| [Resistor 1 kiloohm 0207](https://www.gme.cz/v/1486228/gym-cym-rm-1k-06w-1-0207-metalizovany-rezistor) | 10 | GM Electronic | $0.09 | $0.90 |
| [Resistor 10 kiloohm 0207](https://www.gme.cz/v/1494150/gym-cym-rm-10k-06w-1-0207-metalizovany-rezistor) | 10 | GM Electronic | $0.09 | $0.90 |
| [Shunt resistor 0.1 ohm 3W](https://www.gme.cz/v/1486965/hitano-rd-0r1-3w-5-0617-dratovy-rezistor) | 4 | GM Electronic | $0.22 | $0.88 |
| [Texas Instruments LM324N op-amp](https://www.gme.cz/v/1490186/texas-instruments-lm324n) | 1 | GM Electronic | $0.42 | $0.42 |
| [10 kiloohm potentiometer](https://www.gme.cz/v/1494596/pc1623nbk010-potenciometr) | 2 | GM Electronic | $1.04 | $2.08 |
| [Green LED 3mm](https://www.gme.cz/v/1492964/bright-led-bl-b2141-l-led-3mm-zelena) | 1 | GM Electronic | $0.17 | $0.17 |
| [Red LED 3mm](https://www.gme.cz/v/1492965/bright-led-bl-b5141-l-led-3mm-cervena) | 1 | GM Electronic | $0.17 | $0.17 |
| [KLS7 DPDT push button](https://www.gme.cz/v/1500951/kls-7-p80x80-0-non-lock-tlacitko-do-dps-2-poly-onon) | 1 | GM Electronic | $0.25 | $0.25 |
| [MTS-102-A2 toggle switch](https://www.gme.cz/v/1501004/mts-102-a2-packovy-spinac) | 1 | GM Electronic | $0.66 | $0.66 |
| [DEGSON DG301 screw terminal](https://www.gme.cz/v/1500295/degson-dg301-50-02p-13-00zh-svorkovnice-2pol-roztec-5mm-175a-250v-vstup-90-sroub) | 2 | GM Electronic | $0.15 | $0.30 |
| [Littelfuse RUE300 resettable fuse](https://www.gme.cz/v/1512378/littelfuse-rue300-vratna-pojistka-polyswitch) | 1 | GM Electronic | $0.75 | $0.75 |
| [CA-028-10A volt/amp meter](https://www.gme.cz/v/1518962/creatall-ca-028-10a-digitalni-panelovy-voltmetr-a-ampermetr) | 1 | GM Electronic | $3.73 | $3.73 |
| [DS-026A DC jack 5.5/2.5](https://www.gme.cz/v/1498554/ds-026a-dc-konektor-55-25) | 1 | GM Electronic | $1.04 | $1.04 |
| [M3x10 screw](https://www.gme.cz/v/1511747/wurth-skm3x10-ocelovy-sroub-m3x10mm-phillips-valcova-hlava-pozinkovany) | 5 | GM Electronic | $0.14 | $0.70 |
| [M3 nut](https://www.gme.cz/v/1511808/wurth-skm3k-matice-m3) | 1 | GM Electronic | $0.03 | $0.03 |
| **Parts subtotal** | | | | **$27.33** |
| Shipping (estimate) | | | | $11.00 |
| **Total** | | | | **$38.33** |

I already own the laptop charger so I am not including it. The order quantities are a bit higher somewhere as GM Electronic has minimum order quantities. I will do 3D printing at school. The total exceeds tier 1 so I will cover the rest.

## Printing

- PLA, 0.4mm nozzle, 0.2mm layers, 20% infill
- Tray is printed with the floor on the bed, no supports needed
- Knobs, print the top face on the bed so the shaft hole points up.
- Print 2x foot_12mm and 2x foot_4mm flat side down.

## Assembly

1) Order the PCB from JLCPCB using the gerbers.
2) Some back side parts sit underneath the potentiometers. Solder these first and cut their legs short.
3) Before soldering the SHOW LIMIT button, check it with a multimeter. Make sure the middle pin connects to the outer pin when it's not pressed. If not, rotate the button 180 degrees.
4) Solder the rest of the back side (resistors, capacitors (watch polarity), diodes, TL431, LM324, MOSFET, fuse, inductor, input screw terminal).
5) Screw the LM2576 to the heatsink using the M3 screw and nut, then solder it.
6) Put a piece of electrical tape on the bottom of each potentiometer so its metal tabs can't touch solder joints below it. Then solder the pots, toggle switch, button, LEDs, output terminal on the front side of the PCB.
7) Cut the plugs off the meter's wires and solder them to the 5 meter pads. From pad 1, solder thin red, thin black, thin yellow, thick red, thick black. Check against datasheet/leaflet. Then clip the meter into the cutout.
8) Glue the 12mm feet under the meter side and 4mm feet under the knob side.
9) Mount the DC jack in the back wall of the tray. Drill it bigger if needed. Wire it to the input terminal. Center pin is positive.
10) Put the PCB in the tray and screw it down. Press the knobs onto the potentiometers.

## Usage

1. Plug in the laptop charger, the green POWER LED and meter should be turned on.
2. With OUTPUT off, turn VOLTAGE until the meter shows the desired voltage.
3) Hold SHOW LIMIT and turn CURRENT until the meter shows your desired limit.
4) Connect your circuit and switch OUTPUT on.

## AI disclosure

LLMs (Claude Opus 5.5) were used to plan the concept, get feedback on the schematic/routing, review my work and help find and fix bugs. They were also used for other tedious but simple tasks, like adding the BOM to this README. AI was not used in writing the README, Journal or other parts of the project.

---

Built for [Hack Club Half Life](https://halflife.hackclub.com/) Week 5 (Design Your Own).
