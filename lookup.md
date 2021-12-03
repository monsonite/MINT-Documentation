## Looking up the ASCII value for any char

use `\^`

```
> \^A.
65
```

## Looking up the address of a user definition

User definitions are stored in a 16-bit array starting at an address pointed to by user var \2. To lookup the address of a definition index into this array and read the value of the pointer stored there.

Example for the def

```
:D `Hello!` ;
```

To look up the address of any def

```
:L \^A - { \2 + @;     \\ char -- adr
```

This def accepts a character code and returns an address

To return the address of the def pointed to by D.
\G runs the definition.

```
> \^D L \G
Hello!
```

Here's a breakdown of how L works

```
:L                  \\ define L
\^A -               \\ number of definition
{                   \\ multiply by 2
\2                  \\ address of defs table
+                   \\ address of def ptr
@                   \\ address of def
;                   \\ end of def
```

## Looking up the address of an opcode

Opcodes are pointed to by offsets in a byte array pointed to by user var \6. To lookup the address of an opcode, index into this array and use this offset.

Example: find the address of opcode `

```
:I \6@ #100 + ;             \\ -- adr
:J \^A - \6@ + @ I + ;      \\ char -- adr
```

This def accepts a character code and returns an address

To return the address of the def pointed to by `.

```
> \^` J \XHello!`
Hello!
```
\X executes the opcode.
