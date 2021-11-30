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

