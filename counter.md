# Counter

Problem: make a hexadecimal counter that displays on the upper 4 digits of a 6 digit 7 segment display.

The display is controlled by two ports:

- Port 1 (SCAN) that turns on one or more of the digits. Bit 0 turns on the rightmost digit. Bit 5 turns on the leftmost digit. One additional complication: we need to keep the 6th bit of Port 1 high at all times.
- Port 2 (DISPLAY) turns on and off segments and the decimal point of the currently active display(s).

The numbers `0` - `F` are represented in 7 segments by the following table:

```
DB $EB $28 $CD $AD $2E $A7 $E7 $29 $EF $2F $6F $E6 $C3 $EC $C7 $47
```

The displays need to be scanned rapidly to prevent the perception of flickering. Every second (approximately) the counter is incremented

## Byte array

The first step is to declare a nibble-to-7segments table as a one dimensional byte array

```
\[#EB #28 #CD #AD #2E #A7 #E7 #29 #EF #2F #6F #E6 #C3 #EC #C7 #47]' c!
```

- `\[` indicates that the numbers following are byte values which will be stored in a byte array allocated on the heap.
- `#EB` is an example of a hexadecimal byte value.
- `]` indicates the end of the array. This pushes the address of the array on the stack followed by its length.
- `'` we don't need the length so we drop it.
- `c!` we store the 16-bit address of the array in the variable `c` so we can access it later.

## Definition A: convert a nibble to 7 segment display representation

```
value -- DISPLAY
```

Write a definition which takes a value in the lower 4 bits 0 - F and converts it to 7 segment display representation

```
:A #0F& c@+ \@;
```

Where:

- `:A` declare a definition called A
- `#0F&` bitwise-AND the top of the stack with the hexadecimal value 0F, this mask everything except the bottom 4 bits
- `c@+` get the address of the byte array and add it to the masked nibble value, this is the address of the 7 segment value
- `\@` read a byte from the address
- `;` end of definition

## Definition B: output a nibble to an active segment

Write a definition which takes a 16-bit value and an 8-bit value representing the currently active digit.
We are only interested in the lowest 4 bit of the value. The digit is selected by a 1 in Bit 5 to Bit 0. Bit 6 is kept at 1 at all times.

```
number scan --
```

```
:B $ A 2\O #40 | 1\O 10() #40 1\O;
```

Where:

- `:B` declare a definition called B
- `$` swap `number` with `scan`
- `A` convert the lower 4 bits of `number` into 7 segment representation
- `2\O` write the 7 segments data out to Port 2 (DISPLAY)
- `#40 |` bitwise-OR `scan` value with hex 40 to keep bit 6 high
- `1\O` write digit selector value to Port 1 (SCAN)
- `10()` delay for about half a millisecond
- `#40` output all 0s to the digits but bit 6 kept high
- `1\O` write digit selector value to Port 1 (SCAN)
- `;` end of definition

## Definition C: select next digit, shift nibble down

Write a definition which takes a number and a byte that selects the currently active digit. Replace it with the number shifted right by 4 bits and digit selector bit shifted one bit to the left

```
number scan -- number' scan'
```

```
:C { $ }}}} $;
```

Where:

- `:C` declare a definition called C
- `{` shift `scan` to one bit to left
- `$` swap `number` to top of stack
- `}}}}` shift `number` one nibble right
- `$` swap new `scan` to top of stack
- `;` end of definition

## Definition E: scan number to display

Take a 16-bit number and display it on the upper 4 7-segment displays.

```
number --
```

```
:E #04 4(%%BC)'' ;
```

Where:

- `:E` declare a definition called E
- `#04` push the first digit to scan, 4 is the third-last digit
- `4(` start a loop which will iterate 4 times
- `%%` duplicate the top two stack items
- `B` output the lowest 4 bits of number to active segment
- `C` select next digit, shift nibble down
- `)` end loop
- `''` drop the top two stack items
- `;` end of definition

## Definition F: scan number to display

Take a 16-bit number and display it on the upper 4 7-segment displays.

```
number --
```

```
:F 100(" E)';
```

Where:

- `:F` declare a definition called F
- `100(` loop 100 times
- `"` duplicate `number`
- `E` scan number to display
- `)` end loop
- `'` drop `number`
- `;` end of definition

## Count and display

This is the entry point of the program

Count up from zero displaying the value of the loop variable for a second on each iteration.

To run type:

```
#FFFF( \i@ F ) 0 0B
```

Where:

- `#FFFF(` loop FFFF times
- `\i@` read the value of loop variable `i`
- `F` scan the value of `i` to the displays
- `)` end of loop
- `0 0B` turn off Ports 1 & 2 but keeping bit 6 of Port 1 high
