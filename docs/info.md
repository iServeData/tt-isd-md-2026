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
| **A** | `.-` | 1 | · | · | · | · | ● |
| **B** | `-...` | 2 | · | · | · | ● | · |
| **C** | `-.-.` | 3 | · | · | · | ● | ● |
| **D** | `-..` | 4 | · | · | ● | · | · |
| **E** | `.` | 5 | · | · | ● | · | ● |
| **F** | `..-.` | 6 | · | · | ● | ● | · |
| **G** | `--.` | 7 | · | · | ● | ● | ● |
| **H** | `....` | 8 | · | ● | · | · | · |
| **I** | `..` | 9 | · | ● | · | · | ● |
| **J** | `.---` | 10 | · | ● | · | ● | · |
| **K** | `-.-` | 11 | · | ● | · | ● | ● |
| **L** | `.-..` | 12 | · | ● | ● | · | · |
| **M** | `--` | 13 | · | ● | ● | · | ● |
| **N** | `-.` | 14 | · | ● | ● | ● | · |
| **O** | `---` | 15 | · | ● | ● | ● | ● |
| **P** | `.--.` | 16 | ● | · | · | · | · |
| **Q** | `--.-` | 17 | ● | · | · | · | ● |
| **R** | `.-.` | 18 | ● | · | · | ● | · |
| **S** | `...` | 19 | ● | · | · | ● | ● |
| **T** | `-` | 20 | ● | · | ● | · | · |
| **U** | `..-` | 21 | ● | · | ● | · | ● |
| **V** | `...-` | 22 | ● | · | ● | ● | · |
| **W** | `.--` | 23 | ● | · | ● | ● | ● |
| **X** | `-..-` | 24 | ● | ● | · | · | · |
| **Y** | `-.--` | 25 | ● | ● | · | · | ● |
| **Z** | `--..` | 26 | ● | ● | · | ● | · |

## External hardware

- Two pushbuttons with 10 kΩ pull-down resistors, on ui[0] and ui[1].
- Seven LEDs with 1 kΩ resistors, on uo[0]–uo[4], uo[6] and uo[7].
- A 10 Hz clock.
