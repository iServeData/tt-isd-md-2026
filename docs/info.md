<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

The chip decodes Morse code from one key. It shows the letter as a number from 1 (A) to 26 (Z) on five LEDs.

The design runs on a 10 Hz clock and has four sections.

### Tap timer

A 2-bit counter counts clock ticks while TAP is held and stops at 3.

- Under 3 ticks (about 0.3 s) is a dot.
- 3 ticks or more is a dash.

### Release

A flip-flop remembers TAP from the previous tick. It produces a one-tick pulse when TAP is let go. That pulse is the moment the tap is judged as a dot or a dash.

### Letter steps

There is one flip-flop per letter, arranged as the Morse tree. A letter turns on when the right kind of tap ends while its parent letter is on. The parent is the code minus its last tap; for example, B `-...` follows D `-..`.

Any other tap turns the letter off. A "Started" flip-flop keeps a new letter from beginning in the middle of a pattern. Releasing RECEIVE clears every step.

### Number LEDs

Each output is an OR of the letters whose number has that bit set.

The design has 30 flip-flops and 151 logic gates.

## How to test

1. Hold RECEIVE (ui[0]). READY_LED (uo[6]) turns on.
2. While holding RECEIVE, tap the letter on TAP (ui[1]):
   - Short press = dot.
   - About 1 s = dash.
   - TAP_LED (uo[7]) lights on each press.
3. Add the values of the lit LETTER LEDs: 16, 8, 4, 2 and 1.
   - Example: M `--` lights 8 + 4 + 1 = 13.
4. Release RECEIVE to clear.

All number LEDs off means the pattern isn't a letter.

| Letter | Morse | Number | 16 | 8 | 4 | 2 | 1 |
|---|---|---:|:-:|:-:|:-:|:-:|:-:|
| **A** | `.-` | 1 | Â· | Â· | Â· | Â· | â |
| **B** | `-...` | 2 | Â· | Â· | Â· | â | Â· |
| **C** | `-.-.` | 3 | Â· | Â· | Â· | â | â |
| **D** | `-..` | 4 | Â· | Â· | â | Â· | Â· |
| **E** | `.` | 5 | Â· | Â· | â | Â· | â |
| **F** | `..-.` | 6 | Â· | Â· | â | â | Â· |
| **G** | `--.` | 7 | Â· | Â· | â | â | â |
| **H** | `....` | 8 | Â· | â | Â· | Â· | Â· |
| **I** | `..` | 9 | Â· | â | Â· | Â· | â |
| **J** | `.---` | 10 | Â· | â | Â· | â | Â· |
| **K** | `-.-` | 11 | Â· | â | Â· | â | â |
| **L** | `.-..` | 12 | Â· | â | â | Â· | Â· |
| **M** | `--` | 13 | Â· | â | â | Â· | â |
| **N** | `-.` | 14 | Â· | â | â | â | Â· |
| **O** | `---` | 15 | Â· | â | â | â | â |
| **P** | `.--.` | 16 | â | Â· | Â· | Â· | Â· |
| **Q** | `--.-` | 17 | â | Â· | Â· | Â· | â |
| **R** | `.-.` | 18 | â | Â· | Â· | â | Â· |
| **S** | `...` | 19 | â | Â· | Â· | â | â |
| **T** | `-` | 20 | â | Â· | â | Â· | Â· |
| **U** | `..-` | 21 | â | Â· | â | Â· | â |
| **V** | `...-` | 22 | â | Â· | â | â | Â· |
| **W** | `.--` | 23 | â | Â· | â | â | â |
| **X** | `-..-` | 24 | â | â | Â· | Â· | Â· |
| **Y** | `-.--` | 25 | â | â | Â· | Â· | â |
| **Z** | `--..` | 26 | â | â | Â· | â | Â· |

## External hardware

No extra hardware is needed. The Tiny Tapeout demo board can run the whole project.

### On the board

| Signal | Demo board |
|---|---|
| RECEIVE (ui[0]) | Input DIP switch 1 |
| TAP (ui[1]) | Input DIP switch 2 |
| LETTER_1 (uo[0]) | Segment A (top) |
| LETTER_2 (uo[1]) | Segment B (top right) |
| LETTER_4 (uo[2]) | Segment C (bottom right) |
| LETTER_8 (uo[3]) | Segment D (bottom) |
| LETTER_16 (uo[4]) | Segment E (bottom left) |
| READY_LED (uo[6]) | Segment G (middle) |
| TAP_LED (uo[7]) | Decimal point |

Segment F stays off. The segments don't form a letter. Add up the lit segments to get the letter number.

Set the project clock to 10 Hz in Tiny Tapeout Commander. The design doesn't use reset.

### Optional: breadboard buttons and LEDs

Tapping on a DIP switch is awkward, so you can use real buttons instead. No extra microcontroller is needed; wire the parts straight to the PMOD headers.

- **Buttons:** wire each one 3.3 V -> pushbutton -> input pin.
  - Put a 10 kÎ© pull-down resistor from the same pin to GND.
  - Use ui[0] for Receive and ui[1] for Tap.
  - Use 3.3 V only, never 5 V.
- **LEDs:** wire each one output pin -> 1 kÎ© resistor -> LED -> GND.
  - Use uo[0]-uo[4] for the number, uo[6] for Ready and uo[7] for Tap.
- **Input switches:** keep the on-board input DIP switches off while buttons are connected. Set the board's inputs to manual (`mode = ASIC_MANUAL_INPUTS` in config.ini) so the RP2040 doesn't drive them.
