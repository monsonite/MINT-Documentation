# Chapter 1

## Introduction to MINT

This chapter is only intended as a _Quick Start Guide_. The topics raised in this chapter will be covered in detail in the subsequent chapters.

### What is MINT ?

MINT is a tiny, stack based language inspired by Forth. 

On the Z80 it can be implemented in fewer than 1600 bytes of machine code and it is relatively quick compared to other interpreted languages.

MINT is a form of shorthand. It uses a variety of familiar characters such as `+  -  *`  and `/` to implement arithmetical and logical operations. In total, MINT has about 30 basic instructions consisting of single, printable ascii characters.

These characters will be shown in pale blue highlight to distinguish them from other text.

The characters will be introduced over the next 8 chapters a few at a time. 

If you have come from a background of assembly language, MINT is pitched at a higher level, and allows complex programming tasks to be done without the use of an assembler.

The usual environment is just a serial terminal program running on a laptop or desktop PC. A serial interface cable or adaptor will connect to the target single board computer or _SBC_.  

MINT can be implemented on any microprocessor or microcontroller and once installed, source code can run on any target without modification.

This is achieved by implementing a 16-bit virtual machine, using assembly language on the target processor. Once this interpreter core is created, MINT source code will equally run on a Z80 as it will on a 6502 or any other cpu. The interpreter core is typically between 1K bytes and 2K bytes.

MINT is closely related to Forth, but much smaller in size and less complex. Like Forth it uses a parameter stack which is used to store data in memory using a push-down mechanism.  The stack will be further described in Chapter 2 and Chapter 3.

### Printing Numbers

MINT uses reverse Polish notation (RPN) for it's arithmetic operations. This is a method that was popular on HP electronic calculators.  The main feature of RPN is you have to put the operands before the operator. 

For example, if you want to add two numbers you just type the following at the terminal keyboard:

`123 456 + .`

The numbers `123` then `456` are placed onto the STACK. The `+` command ADDs them together and leaves their sum as a result on the top of the stack. The DOT `.` is a shorthand character to instruct the MINT interpreter to print the result on the top of the stack to the terminal screen.	

When you hit `<ENTER>` the result will be displayed thus
```
00579
>  
``` 
This `>` is the cursor-prompt that confirms that the code has been executed and control has been passed back to the User.

MINT defaults to using DECIMAL arithmetic, but HEXADECIMAL (BASE 16) is also available. More about this in Chapter 3.

Decimal numbers between 0 and 65535 are accepted, and printout is always in the form of a 5 digit number with leading zeros inserted where appropriate.

### Printing Text

Most computer languages are demonstrated by printing the text string `Hello World` on the terminal display. MINT is no exception, and it is very simple to print text to the screen.  Anything enclosed between back ticks ``text`` will be printed
`\N` is the way in which we send a _NEWLINE_ character to the terminal.

```
`Hello World` \N   <ENTER>
Hello World
>
```

# Chapter 2

## Fundamentals

In Chapter 1 we introduced the absolute basics of the MINT interpreter with a couple of very simple examples.

This chapter will further explore the MINT interpreter introducing the basic concepts and how the MINT interpreter works.

Chapters 3 to 9 will introduce the basic language a few commands at a time. Chapter 10 will introduce the extended language commands.

At the end of this document is a Glossary of all the commands used in MINT. Each chapter has an excerpt from the Glossary to summarise the commands that h=ave been discussed in that chapter.

Like other small interpreted languages, the intention of MINT is to create a 16-bit virtual machine by combining the mostly 8-bit operations available on the Z80, to provide 16-bit integer arithmetic and variable handling.

The language needs the basic arithmetic operations of ADD, SUBTRACT, MULTIPLY and DIVIDE. These are implemented as 16-bit integer operations and invoked using the familiar characters `+`, `-`, `*` and `/`.

These are augmented by the bitwise Boolean operators AND, OR, XOR, INVERT and 2's complement NEGATE. 

With MINT, these instructions are just one byte long and a look-up table is used instead of a switch-case structure. When using an 8-bit microprocessor, such as the Z80, it is simpler and faster to handle 8-bit instructions, so MINT uses a bytecode system, rather than the 16-bit threaded code that is used by a conventional Forth.

In the Chapter 1 example above 

```
123 456 + .	<ENTER>
```

The numerical strings `123` and `456` are evaluated as 16-bit binary numbers and placed on the data stack. The plus symbol `+` is interpreted as a jump to the routine that performs a 16-bit addition of the top two elements on the data stack, placing their sum on the top of the data stack. The dot character prints out the top value of the data stack, consuming it at the same time.

In addition to the arithmetic and boolean operations, there are also the three comparison operators:

Greater Than `>`, 

Less Than `<`

Equal `=`

The top two elements on the stack will be compared, resulting in 1 if the comparison is TRUE and 0 if the comparison is FALSE.

With the comparison operators, it becomes possible to develop conditionally executed code, introduced in Chapter 6, which forms the basis of program control words, such as IF, THEN, ELSE, and looping and branching structures.

In total there are approximately 30 characters that are recognised as the internal instruction set, or primitives. From these characters the user can construct further definitions to extend the usefulness of the language.              

### How MINT Works.

MINT is an interpreted language that uses printable ascii characters as its "instructions". There are 95 such characters:

26 Uppercase letters - used as User Commands  `A` to `Z`
26 Lowercase letters - used as User Variables `a` to `z`
10 Numerals - used for number entry			  `0` to `9`	
33 arithmetic and punctuation symbols - used to command the program operation

The interpreter scans a text string, held in a text buffer, one character at a time. It then uses a look-up table to broadly categorise the current character into one of the above groups.

For each category of character there is a handling routine, which determines how the character should be processed.

### NUMBERS

A number string such as 1234 will be scanned one digit at a time and converted into a 16-bit binary number using a routine called num\_ . The converted binary number is then placed on the top of the data stack which is used as a means of temporary storage, before being used later. Multiple numbers may be entered in a sequence separated by spaces: `1234 5678 3579` When the return key is pressed they will be processed in turn and each placed onto the stack. They may then be used as operands or parameters for a calculation or other function.

### VARIABLES

User Variables are assigned to the lowercase alpha characters using a routine called var\_ The user variables are stored in an array of 26 pairs of bytes in RAM. The lowercase character is a shorthand way of addressing the pair of bytes that holds the variable. It is not usually necessary to know specifically at what address the variable is held at, as it can always be accessed using its name.

For example, the variable addressed by the lowercase character `a` is held in RAM locations 34816 and 34817. Variable `b` will be held in the next locations 34818 and 34819 and so on up to `z`.

When a lowercase character is interpreted the variable handler routine converts it to a 16-bit address, and places that address on the top of the stack.

### COMMANDS

User Commands are what gives MINT its power and flexibility. Each uppercase letter is a substitute for an address in RAM, where the users code routines are held. For example you may have a routine which produces a hexadecimal dump of the contents of memory. You choose to use the D command to initiate this routine, D for DUMP. You may also pass parameters to a user routine via the stack. In the case of a hex dump routine it would be common to give it the starting address of the section you want to dump, and this might be written 1234 D. On pressing return, the command will be interpreted and the dump routine will commence printing from location 1234. There are 26 User Commands plus some Alternate User Commands which is usually enough for most small applications.

### PRIMITIVES

A primitive is a built in function, normally stored in ROM and not usually needed to be modified by the User. Primitives will include the familliar mathematical functions such as ADD, SUBtract, MULtiply and DIVide, and also boolean logic operations such as AND, OR, XOR and INVert.

There are also a small group of primitives that perform operations on the stack, DUP is used to duplicate the top item, DROP will remove the top item, making the second item available. SWAP will exchange the top two intems, effectively placing the second item on top.

In total, MINT contains 33 primitives which are executed when the interpreter finds the relevant symbol. Some of these will be commonly used arithmetic symbols like `+` and `-` Others are allocated to punctuation symbols. The full-stop, or dot character `.` is used to print out the number held on the top of the stack.

### Using MINT on the RC2014

MINT was developed for the RC2014 Micro Z80 Single Board Computer. This board is supplied with a comprehensive Monitor program (The Small Computer Monitor (SCM) by Stephen Cousins). A 32K ROM contains the monitor and BASIC between $0000 and $7FFF. The 32K RAM starts at $8000, and MINT is loaded in to run from address $8100.

If necessary, you can use the serial getchar and printchar routines that are available within the Small Computer Monitor

See the User Manual pages 45 and 46 on how this is done

https://smallcomputercentral.files.wordpress.com/2018/05/scmon-v1-0-userguide-e1-0-0.pdf

MINT was assembled using asm80.com, an online 8-bit assembler. It will generate an Intel Hex file that can be pasted into RAM at address $8000 using a serial terminal program. I use TeraTerm when working within the windows environment.

Once the MINT code image is pasted into RAM you can run it using the Go command "G8000"

On initialisation it will present a user prompt `>` followed by a CR and LF. It is now ready to accept commands from the keyboard. MINT currently uses decimal numbers for calculations - a maximum integer of 65535.

MINT has about 30 built in commands called primitives. They are mostly allocated to arithmetical and punctuation symbols.

There are 26 User Defined Commands that use uppercase alpha characters `A`-`Z`

There are 26 User Variables that are assigned to lowercase alpha characters `a`-`z`

MINT turns the Z80 into a 16-bit Virtual Machine with 30 instructions, 26 Macros and 26 Registers (variables). This relieves you from the tedium of Z80 assembly language, and presents the user with a very compact, human readable, interactive, extendable bytecode language.

### Later in this manual

Chapter 3	Number Entry, Basic Arithmetic and Printing

Chapter 4	Stack Operations

Chapter 5	Introducing User Definitions

Chapter 6	Conditional Code Execution and Loops

Chapter 7	Variables

Chapter 8	Arrays

Chapter 9	Boolean and Logical Operations

Chapter 10  Extended and Alternate Commands

Glossary -  A list of all commands used in MINT

