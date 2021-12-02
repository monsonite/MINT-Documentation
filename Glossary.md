## Glossary

### LIST OF PRIMITIVES

Mint is a bytecode interpreter - this means that all of its instructions are 1 byte long. However, the choice of instruction uses printable ASCII characters, as a human readable alternative to assembly language. The interpreter handles 16-bit integers and addresses which is sufficient for small applications running on an 8-bit cpu.

There are roughly 30 punctuation and arithmetical symbols available in the printable ASCII codes. These are assigned to the primitive functions, from which more complex programs can be built.

### Maths Operators

| Symbol | Description                               | Effect   |
| ------ | ----------------------------------------- | -------- |
| +      | 16-bit integer addition ADD               | a b -- c |
| -      | 16-bit integer subtraction SUB            | a b -- c |
| \*     | 8-bit by 8-bit integer multiplication MUL | a b -- c |
| /      | 16-bit by 8-bit division DIV              | a b -- c |
| <      | 16-bit comparison LT                      | a b -- c |
| =      | 16 bit comparison EQ                      | a b -- c |
| >      | 16-bit comparison GT                      | a b -- c |
| {      | shift the number to the left (2\*)        | a -- b   |
| }      | shift the number to the right (2/)        | a -- b   |
| \\b    | base 16 flag variable                     | -- a     |
| \\\_   | sign of number                            | n -- b   |
| \\M    | maximum                                   | a b -- m |
| \\m    | minimum                                   | a b -- m |

### Logical Operators

| Symbol | Description                          | Effect   |
| ------ | ------------------------------------ | -------- |
| ~      | 16-bit bitwise inversion INV         | a -- b   |
| \_     | 16-bit negation (2's complement) NEG | a -- b   |
| &      | 16-bit bitwise AND                   | a b -- c |
| \|     | 16-bit bitwise OR                    | a b -- c |
| ^      | 16-bit bitwise XOR                   | a b -- c |

Note: logical NOT can be achieved with 0=

### Stack Operations

| Symbol | Description                                                                   | Effect       |
| ------ | ----------------------------------------------------------------------------- | ------------ |
| "      | duplicate the top member of the stack DUP                                     | a -- a a     |
| '      | drop the top member of the stack DROP                                         | a a -- a     |
| $      | swap the top 2 members of the stack SWAP                                      | a b -- b a   |
| %      | over - take the 2nd member of the stack and copy it onto the top of the stack | a b -- a b a |

### Input & Output Operations

| Symbol | Description                                               | Effect      |
| ------ | --------------------------------------------------------- | ----------- |
| #      | the following number is in hexadecimal                    | a --        |
| .      | print the top member of the stack as a decimal number DOT | a --        |
| ,      | print the number on the stack as a hexadecimal            | a --        |
| \`     | \`Everything between ticks is printed as a string\`       | --          |
| \\$    | text input pointer variable                               | -- adr      |
| \\E    | emits a char to output                                    | val --      |
| \\I    | input from a I/O port                                     | port -- val |
| \\K    | read a char from input                                    | -- val      |
| \\N    | prints a CRLF to output                                   | --          |
| \\O    | output to an I/O port                                     | val port -- |
| \\P    | non-destructively prints stack                            | --          |
| \\Z    | print definition by number                                | n --        |

### User Definitions

| Symbol | Description                | Effect |
| ------ | -------------------------- | ------ |
| :      | define a new word DEF      | "C"    |
| ;      | end of user definition END |        |

NOTE: "C" is an uppercase letter immediately following opcode which is the name of the definition

### Loops and conditional execution

| Symbol | Description                                       | Effect |
| ------ | ------------------------------------------------- | ------ |
| (      | BEGIN a loop or conditionally executed code block | n --   |
| )      | END a loop or conditionally executed code block   | --     |
| \\(    | Execute if false, preserves condition             | n -- n |
| \\i    | returns index variable of current loop            | -- val |
| \\j    | returns index variable of outer loop              | -- val |
| \\W    | if false then skip to end of loop                 | b --   |

### Memory and Variable Operations

| Symbol | Description                                 | Effect        |
| ------ | ------------------------------------------- | ------------- |
| @      | FETCH a value from memory                   | -- val        |
| !      | STORE a value to memory                     | val adr --    |
| \\+    | increments variable at address by an amount | val adr --    |
| \\-    | decrements variable at address by an amount | val adr --    |
| \\@    | FETCH a byte from memory                    | -- val        |
| \\!    | STORE a byte to memory                      | val adr --    |
| [      | begin an array definition                   | --            |
| ]      | end an array definition                     | -- adr nwords |
| \\[    | begin a byte array definition               | --            |
| \\h    | heap pointer variable                       | -- adr        |
| \\$    | text input buffer pointer variable          | -- adr        |
| \\B    | base16 flag variable                        | -- adr        |

### Constants and variables

| Symbol | Description                 | Effect |
| ------ | --------------------------- | ------ |
| \\0    | start address of data stack | -- adr |
| \\1    | text input buffer address   | -- adr |
| \\2    | defs address                | -- adr |
| \\3    | vars address                | -- adr |
| \\4    | user vars address           | -- adr |
| \\5    | macros address              | -- adr |
| \\6    |                             | -- adr |
| \\7    |                             | -- adr |
| \\8    |                             | -- adr |
| \\9    | temp variable               | -- adr |

### Miscellaneous

| Symbol | Description                                   | Effect   |
| ------ | --------------------------------------------- | -------- |
| \\\\   | comment text, skips reading until end of line | --       |
| \\G    | execute mint code at address                  | adr -- ? |
| \\Q    | quits from Mint interpreter                   | --       |
| \\X    | execute machine code at address               | adr -- ? |

### Control keys

| Symbol | Description                     |
| ------ | ------------------------------- |
| ^B     | toggle base decimal/hexadecimal |
| ^E     | edit a definition               |
| ^H     | backspace                       |
| ^L     | list definitions                |
| ^P     | print stack                     |
